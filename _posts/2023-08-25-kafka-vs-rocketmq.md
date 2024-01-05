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

4. 消费方式，Pull 拉取式消费，Consumer 需要主动拉取 Broker 中的消息；Push 推送式消费，Broker 一接收到消息马上发送给 Consumer，具有实时性。RocketMQ是基于 Pull 模式的**长轮询**策略实现消息消费的。即 Consumer 发送拉取请求到 Broker 端，如果 Broker 有数据则返回并继续发起长轮询，如果没有则 hold 请求，不立即返回，直到超时（默认5s）并继续发起长轮询。为什么需要设置超时？1. 无法避免服务器假死等情况以确保可用性；2. 可能修改监听的配置

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/69eb913379e6407da05a7b13d9035976~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp)

5. RocketMQ 提供多种发送方式：同步发送、异步发送、单向发送。其中同步发送和异步发送需要 Broker 返回确认消息，而单向发送不需要。

6. 消费者群组：一个消费者组可以消费多个 Topic 的消息，组内的消费者只能订阅相同的 Topic 和相同的 tag且 Tag 顺序相同。详见[订阅关系一致](https://help.aliyun.com/zh/apsaramq-for-rocketmq/cloud-message-queue-rocketmq-4-x-series/use-cases/subscription-consistency)。支持[两种消费方式](https://juejin.cn/post/7089788861915594766)：集群消费（默认）和广播消费。集群消费，相同消费者群组的消费者平摊消息，便于负载均衡；广播消费，相同消费者群组的每个消费者接收全量的消息，适合并行处理的场景。

7. 

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


## 吞吐量

- Kafka 单机吞吐量 TPS 可上百万，远高于 RocketMQ的 TPS 十万级。

## 数据可靠性

[RocketMQ新增同步刷盘和同步复制机制](https://www.cnblogs.com/toUpdating/p/10021372.html)，保证可靠性。
- Kafka 支持异步刷盘，异步复制。
- RocketMq 支持异步刷盘和同步刷盘，同步复制和异步复制。

异步刷盘：返回写成功状态时，消息可能只是被写进内存，**吞吐量大**，当内存大消息积累到一定程度时，统一触发写磁盘操作，快速写入。

同步刷盘：返回写成功状态时，消息已经被写入磁盘。流程是消息写入内存后，立刻通知刷盘线程刷盘，等待刷盘完成后再唤醒等待的线程返回消息写成功的状态。

异步复制：只要写就返回写成功状态。较低的延迟和较高的吞吐量。

同步复制：写成功后返回写成功状态。容易恢复故障的数据。

## 零拷贝
实现方式
1. mmap(Memory Mapped Files) + write，作用：将磁盘文件映射到内存, 用户通过修改内存就能修改磁盘文件。Java NIO里对应的是MappedByteBuffer类，可以用来实现内存映射。它的底层是调用了Linux内核的mmap的API。
2. sendfile，实现：将读到内核空间的数据，转到socket buffer，进行网络发送。FileChannel的transferTo()/transferFrom()，底层就是sendfile() 系统调用函数。

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

## 事务
- Kafka 不支持事务
- RocketMQ 支持事务，采用二阶段提交+broker定时回查。但也只能保证生产者与broker的一致性，broker与消费者之间只能单向重试。即保证的是最终一致性。

[对于一个大事务来说，可以划分成多个小事务异步执行。](https://dbaplus.cn/news-21-1123-1.html)

![](https://rocketmq.apache.org/zh/assets/images/transflow-0b07236d124ddb814aeaf5f6b5f3f72c.png)

二阶段：第一阶段发送 prepared 消息，接着执行本地事务，第二阶段发送 commit 或 rollback 的消息。

定时回查：定时遍历 commitlog 中的半事务消息

如果事务正常执行，则 commit 该消息，如果抛出异常，则 rollback。对于消费消息失败，RocketMQ 会尝试重新消费，直到被加入死信队列中为止。在重试的过程中有可能产生重复的消息，所以对于消费端来说要确保**消费幂等**！


## 服务发现
- Kafka 使用 ZooKeeper，但新版本使用内嵌的KRaft替代了ZooKeeper
- RocketMQ 使用自己实现的 nameserver


## Kafka 如何保证消息的顺序性
针对消息有序的业务需求，还分为全局有序和局部有序。已知，每个partition的消费是顺序性的，但每个topic可以有若干个partition。

全局有序：一个Topic下的所有消息都需要按照生产顺序消费。

解决方法：1个Topic只能对应1个Partition。

局部有序：一个Topic下的消息，只需要满足同一业务字段的要按照生产顺序消费。例如：Topic消息是订单的流水表，包含订单orderId，业务要求同一个orderId的消息需要按照生产顺序进行消费。

解决方法：要满足局部有序，只需要在发消息的时候指定Partition Key，Partition Key相同的消息会放在同一个Partition。

