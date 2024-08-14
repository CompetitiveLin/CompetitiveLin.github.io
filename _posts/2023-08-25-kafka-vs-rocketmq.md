---
title: Kafka vs RocketMQ
categories: []
tags: [backend]
date: 2023-08-25T17:10:22+800
last_modified_at: 
pin: false
---

# 基本概念

1. RocketMQ 由 Producer, Brocker, Consumer 组成
- Producer 负责生产消息
- Consumer 负责消费消息
- Broker 负责存储消息，每一个 Broker 对应一台服务器但可以存储多个 Topic 的消息，每个 Topic 的消息也分片存储在不同的 Broker 里。

2. Topic 是逻辑概念，队列（Kafka 中叫分区）是物理概念。每个主题包含多个消息，每条消息只属于一个主题。一个 Producer 可以同时发送多种 Topic 的消息，而一个 Consumer 只能订阅一个 Topic 的消息。Tag 类似于子主题。

3. MessageQueue用于存储消息的物理地址，每个Topic中的消息地址存储于多个MessageQueue，是消息的最小存储单元。

4. 消费方式，Pull 拉取式消费，Consumer 需要主动拉取 Broker 中的消息；Push 推送式消费，Broker 一接收到消息马上发送给 Consumer，具有实时性。RocketMQ是基于 Pull 模式的**长轮询**策略实现消息消费的。即 Consumer 发送拉取请求到 Broker 端，如果 Broker 有数据则返回并继续发起长轮询，如果没有则 hold 请求（指的服务端暂时不回复结果，保存相关请求，不关闭请求连接），不立即返回，直到超时（默认5s）并继续发起长轮询。为什么需要设置超时？1. 无法避免服务器假死等情况以确保可用性；2. 可能修改监听的配置

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/69eb913379e6407da05a7b13d9035976~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp)

5. RocketMQ 提供多种发送方式：同步发送、异步发送、单向发送。其中同步发送和异步发送需要 Broker 返回确认消息，而单向发送不需要。


## 消息队列的三大作用
- 解耦
- 异步
- 削峰

## 模式
- 点对点模式：基于队列，每条消息只能被一个消费者消费，RabbitMQ。
- 发布/订阅模式：一条消息能被多个消费者消费，RocketMQ 和 Kafka。


# [区别](https://blog.csdn.net/shijinghan1126/article/details/104724407)

整体区别是 Kafka 的设计初衷是用于日志传输，而 RocketMQ 是用于解决各类应用可靠的消息传输，适用于业务需求。

## 存储形式

- Kafka 采用partition，每个topic的每个partition对应一个文件。顺序写入，定时刷盘。但一旦单个broker的partition过多，则顺序写将退化为随机写，Page Cache脏页过多，频繁触发缺页中断，性能大幅下降。

- RocketMQ 采用CommitLog + ConsumeQueue，物理存储文件是CommitLog，ConsumeQueue是消息的逻辑队列，类似数据库的索引文件，存储的是指向物理存储的地址。每个Topic下的每个MessageQueue都有一个对应的ConsumeQueue文件，单个broker所有topic在CommitLog中**顺序写**。每个CommitLog大小固定为1G。

生产消息：Producer 先向 CommitLog 顺序写，持久化后将数据 Dispatch 到 ConsumeQueue 中。
消费消息：Consumer 从 ConsumeQueue 中拉取数据，但拉取到数据是指向 CommitLog 的地址，此时是随机读，但又因为 PageCache 的存在，还是整体有序的。

Page Cache（页面缓存）从内存中划出一块区域缓存文件页，如果要访问外部磁盘上的文件页，首先将这些页面拷贝到内存中，再进行读写。

![](https://pic4.zhimg.com/80/v2-21afcaf0496e6498d181522fd1a0154b_1440w.webp)

## 吞吐量

- Kafka 单机吞吐量 TPS 可上百万，远高于 RocketMQ的 TPS 十万级。

## 数据可靠性

[RocketMQ新增同步刷盘和同步复制机制](https://www.cnblogs.com/toUpdating/p/10021372.html)，保证可靠性。而 Kafka 倾向于牺牲部分可靠性换取更高的性能（因为 Kafka 中的 producer 将信息堆起来一起发送，以减少网络IO，但是这个时候如果 producer 宕机了，会导致信息丢失的）。
- Kafka 支持异步刷盘，异步复制。
- RocketMq 支持异步刷盘和**同步刷盘**，同步复制和异步复制。

异步刷盘：返回写成功状态时，消息可能只是被写进内存，**吞吐量大**，当内存大消息积累到一定程度时，统一触发写磁盘操作，快速写入。

同步刷盘：返回写成功状态时，消息已经被写入磁盘。流程是消息写入内存后，立刻通知刷盘线程刷盘，等待刷盘完成后再唤醒等待的线程返回消息写成功的状态。

异步复制：只要写就返回写成功状态。较低的延迟和较高的吞吐量。

同步复制：写成功后返回写成功状态。容易恢复故障的数据。

## 如何保证消息不丢失

两者类似，都是从 Producer -> Broker -> Consumer 三个阶段逐一判断，只不过设置的参数不同。

- Producer: 同步发送消息；超时重试发送；消息补偿机制（Kafka，超时仍失败的情况下，会继续投递到本地消息表，定时轮询并推送到Kafka）

- Broker: 同步刷盘；设置主从模式，配置副本

- Consumer: At least Once 的消费机制；消费重试；手动确认消息（Kafka）


## 零拷贝

传统的数据传输过程通常需要经历多次内存拷贝。

首先，从磁盘读取数据，然后将数据从内核空间拷贝到用户空间，再从用户空间拷贝到应用程序的内存中。这些额外的拷贝会消耗大量的CPU资源和内存带宽，降低数据传输的效率。零拷贝就是为了避免这些不必要的数据拷贝，能够将数据直接传输到目标内存区域，以提高数据传输的效率。

实现方式
1. mmap(Memory Mapped Files) + write，作用：将磁盘文件映射到内存, 用户通过修改内存就能修改磁盘文件。Java NIO里对应的是MappedByteBuffer类，可以用来实现内存映射。它的底层是调用了Linux内核的mmap的API。
2. sendfile，实现：将读到内核空间的数据，拷贝到socket buffer，进行网络发送。避免了数据在内核和用户空间之间的额外拷贝。FileChannel的transferTo()/transferFrom()，底层就是sendfile() 系统调用函数。

零拷贝技术减少了用户进程地址空间和内核地址空间之间由于上下文切换而带来的开销。DMA (Direct Memory Access) 是零拷贝技术的基石。并不是不需要拷贝，而是减少冗余不必要的拷贝。
- Kafka： Producer生产的数据持久化到 broker 采用 mmap 文件映射，实现顺序的快速写入；而 Customer 从 broker 读取数据采用 sendfile 进行网络发送。
- RocketMQ：采用 mmap 的方法。正因为使用内存映射机制，RocketMQ的文件存储都使用定长结构来存储，方便一次将整个文件映射至内存。

为什么 Kafka 这么快？

答：四个要点，顺序读写、零拷贝、消息压缩、[分批发送](https://xie.infoq.cn/article/60f221598d38442575d6fa5e0)。

为什么 RocketMQ 这么快？

答：顺序写，零拷贝，异步刷盘（先写入操作系统的PageCache再异步刷盘到磁盘）


## 消息失败重试
- Kafka 不支持重试。
- RocketMQ 支持定时重试，每次重试间隔逐渐增加。

RocketMQ 的消费重试是基于**延迟消息**实现的，在消息消费失败的情况下，重新当作延迟消息投递到 Broker 中，并且延迟等级逐渐增加，消息重试会有 16 个级别，恰好是延迟消息的 18 个级别的后 16 个级别。

### Rocket延迟消息

主要分为以下几步，类似于流转：
1. 修改消息的Topic名称和队列信息（因为发送到普通的Topic会马上被消费）
2. 转发消息到延迟Topic为SCHDULE_TOPIC_XXX的ConsumeQueue中
3. 延迟服务（本质是Java自带的延迟队列）消费SCHDULE_TOPIC_XXX的消息
4. 将信息重新存储到CommitLog中
5. 将消息投递到目标Topic中
6. 消费Topic的消息

### Kafka 延迟消息

基于 **时间轮** 算法

## 事务
- Kafka 支持事务（更像是原子性），可以实现对多个 Topic 、多个 Partition 的原子性的写入，即处于同一个事务内的所有消息，最终结果是要么全部写成功，要么全部写失败。实际上是配合其幂等机制来实现 Exactly Once 语义的。
- RocketMQ 支持事务，采用二阶段提交+broker定时回查。但也只能保证生产者与broker的一致性，broker与消费者之间只能单向重试。即保证的是最终一致性。

[对于一个大事务来说，可以划分成多个小事务异步执行。](https://dbaplus.cn/news-21-1123-1.html)

![](https://rocketmq.apache.org/zh/assets/images/transflow-0b07236d124ddb814aeaf5f6b5f3f72c.png)

二阶段：第一阶段发送 prepared 消息，接着执行本地事务，第二阶段发送 commit 或 rollback 的消息。

定时回查：定时遍历 commitlog 中的半事务消息

如果事务正常执行，则 commit 该消息，如果抛出异常，则 rollback。对于消费消息失败，RocketMQ 会尝试重新消费，直到被加入死信队列中为止。在重试的过程中有可能产生重复的消息，所以对于消费端来说要确保**消费幂等**！

## 消息堆积

原因可能是以下三种：
1. 生产远超预期
2. 消息接收和持久化出现故障
3. 消费能力下降
4. 程序问题

处理堆积的消息：建立临时的 topic（扩容），转发堆积的消息


## 服务发现
- Kafka 使用 ZooKeeper，但新版本使用内嵌的KRaft替代了ZooKeeper
- RocketMQ 使用自己实现的 nameserver

## Topic 数量对吞吐量的影响

RocketMQ：topic 可以达到几百/几千的级别，吞吐量会有较小幅度的下降，这是 RocketMQ 的一大优势，在同等机器下，可以支撑大量的 topic

Kafka：topic 从几十到几百个时候，吞吐量会大幅度下降，在同等机器下，Kafka 尽量保证 topic 数量不要过多，如果要支撑大规模的 topic，需要增加更多的机器资源



## 消息顺序性

分为两步：生产者有序存储，消费者有序消费。

### Kafka 如何保证消息的顺序性
针对消息有序的业务需求，还分为全局有序和局部有序。已知，每个partition的消费是顺序性的，但每个topic可以有若干个partition。

全局有序：一个Topic下的所有消息都需要按照生产顺序消费。

解决方法：1个Topic只能对应1个Partition。

局部有序：一个Topic下的消息，只需要满足同一业务字段的要按照生产顺序消费。例如：Topic消息是订单的流水表，包含订单orderId，业务要求同一个orderId的消息需要按照生产顺序进行消费。

解决方法：要满足局部有序，只需要在发消息的时候指定Partition Key，Partition Key相同的消息会放在同一个Partition。

### RocketMQ 如何保证消息的顺序性

全局有序：对于指定的一个 Topic，设置读写队列的数量为一。（与Kafka设置一个partition类似）
局部有序：对于指定的一个 Topic，生产者根据 hashKey 将消息发送到同一个MessageQueue。 同一个分区内的消息按照严格的 FIFO 顺序进行发布和消费。

实现消息有序性从三个方面：

1. 生产者生产顺序消息：生产者将消息路由到特定分区，单线程发送消息
2. Broker 保存顺序消息：保证生产者顺序生产即可，保存到指定的Partition或MessageQueue中
3. 消费者顺序消费消息：设置 consumeMode 为 ORDERLY，单线程消费消息



## RocketMQ 负载策略

### Producer 负载均衡

Producer 默认采用轮询的方法，按顺序将消息发送到 MessageQueue 里。

### Consumer 负载均衡

1. 平均负载策略，MessageQueue 总数量除以消费者数量并求余数，前余数个数个消费者增加一个 MessageQueue 的消费

![](https://s6.51cto.com/oss/202203/14/e9f849a39832efee7e833607029c85de3880c4.png)

2. 循环分配策略：循环顺序遍历消费者

![](https://s4.51cto.com/oss/202203/14/760652913a6c25afb83877fa3352da05c6e9dc.png)

3. 指定机房分配策略

![](https://s6.51cto.com/oss/202203/14/62b8d0b98e057beb80d026b5a578de36fe376c.png)

4. 机房就近分配策略

![](https://s2.51cto.com/oss/202203/14/522866530333cbe9bc6074464633c81b3a1775.png)

5. 一致性哈希算法策略

![](https://s9.51cto.com/oss/202203/14/d6a9910256e2acc868e4012618187f853c8a3c.png)

## 适用场景

Kafka：适用于日志收集与分析、实时流处理、大数据集成（例如 Apache Storm 或 Flink）、用户行为追踪等场景。
RocketMQ：更适合金融交易、订单处理、秒杀活动、库存同步、跨系统间的服务解耦和异步调用等场景，尤其是那些对消息顺序、事务完整性和实时性要求极高的业务。


# 消费者

## 消费者群组（Consumer Group）

消费者组是一组共享 `group.id` 的消费者实例，一个消费者组可以消费多个 Topic 的消息，组内的消费者只能订阅相同的 Topic 和相同的 Tag 且 Tag 顺序相同。详见[订阅关系一致](https://help.aliyun.com/zh/apsaramq-for-rocketmq/cloud-message-queue-rocketmq-4-x-series/use-cases/subscription-consistency)。

## 消费方式
1. 集群模式（默认）：相同消费者群组的消费者平摊消息，便于负载均衡
![](https://img2023.cnblogs.com/blog/80824/202302/80824-20230222093917462-1788706428.png)

2. 广播模式：相同消费者群组的每个消费者接收全量的消息，适合并行处理的场景。
![](https://img2023.cnblogs.com/blog/80824/202302/80824-20230222094424631-1247508728.png)

RocketMQ/Kafka 使用 Consumer Group 机制，实现了传统两大消息引擎。如果所有实例属于同一个Group，那么它实现的就是消息队列模型；如果所有实例分别属于不同的Group，且订阅了相同的主题，那么它就实现了发布/订阅模型；

## 消费者和消费者组的关系
1. 同一个消费者组内部的消费者均匀消费订阅的 Topic 的消息，负载均衡
2. 不同消费者组全量消费订阅的 Topic 的消息，类似消费者组层面的广播模式