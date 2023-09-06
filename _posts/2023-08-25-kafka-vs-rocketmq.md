---
title: Kafka vs RocketMQ
categories: []
tags: [backend]
date: 2023-08-25T17:10:22+800
last_modified_at: 
pin: false
---

# 消息队列的三大作用
- 解耦
- 异步
- 削峰

# [区别](https://blog.csdn.net/shijinghan1126/article/details/104724407)

整体区别是 Kafka 的设计初衷是用于日志传输，而 RocketMQ 是用于解决各类应用可靠的消息传输，适用于业务需求。

## 存储形式

- Kafka 采用partition，每个topic的每个partition对应一个文件。顺序写入，定时刷盘。但一旦单个broker的partition过多，则顺序写将退化为随机写，Page Cache脏页过多，频繁触发缺页中断，性能大幅下降。

- RocketMQ 采用CommitLog+ConsumeQueue，单个broker所有topic在CommitLog中顺序写，Page Cache只需保持最新的页面即可。同时每个topic下的每个queue都有一个对应的ConsumeQueue文件作为索引。ConsumeQueue占用Page Cache极少，刷盘影响较小。


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

零拷贝技术减少了用户进程地址空间和内核地址空间之间由于上下文切换而带来的开销。DMA (Direct Memory Access) 是零拷贝技术的基石。可以看出没有说不需要拷贝，只是说减少冗余不必要的拷贝。
- Kafka： Producer生产的数据持久化到 broker 采用 mmap 文件映射，实现顺序的快速写入；而 Customer 从 broker 读取数据采用 sendfile 进行网络发送。
- RocketMQ：采用 mmap 的方法。 

为什么 Kafka 这么快？

答：四个要点，顺序读写、零拷贝、消息压缩、[分批发送](https://xie.infoq.cn/article/60f221598d38442575d6fa5e0)。


## 消息失败重试
- Kafka 不支持重试。
- RocketMQ 支持定时重试，每次重试间隔逐渐增加。

## 事务
- Kafka 不支持事务
- RocketMQ 支持事务，采用二阶段提交+broker定时回查。但也只能保证生产者与broker的一致性，broker与消费者之间只能单向重试。即保证的是最终一致性。

## 服务发现
- Kafka 使用 ZooKeeper，但新版本使用内嵌的KRaft替代了ZooKeeper
- RocketMQ 使用自己实现的 nameserver


# RocketMQ

## NameServer

