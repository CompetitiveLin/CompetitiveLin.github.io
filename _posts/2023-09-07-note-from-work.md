---
title: Note from Work
categories: []
tags: [backend]
date: 2023-09-07T10:41:22+800
last_modified_at: 
pin: false
---

1. Grafana 的数据显示会五分钟自动补全。当向 Prometheus 中插入某个时间戳的值时，其值会延续五分钟。

2. K8S 中的 Sidecar 模式：通常情况下一个 Pod 只包含一个容器，但是 Sidecar 模式是指为主容器**提供额外功能（例如监控）** 从而将其他容器加入到同一个 Pod 中。再例如 [Istio 实现 Sidecar 自动注入](https://juejin.cn/post/6951567887664414757)。

3. Federated cluster，联邦集群

4. 限流算法：漏桶和令牌桶算法，漏桶算法处理请求的速度固定，突发请求过多时会丢弃；令牌桶算法除了限制数据的平均传输速率外，还要求允许某种程度的突发传输。

5. [常见 HTTP 状态码](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status)：2XX，成功响应；3XX，重定向消息；4XX，客户端错误响应；5XX，服务端错误响应。

6. 请求分为四部分：请求行，请求头，空行，请求正文（不一定有）。

7. `.gitignore` 是在文件从工作区被 **add** 到暂存区时判断是否要被忽略，对于已经在暂存区的文件，不作判断。

## 常见NoSQL数据库类型
1. 键值数据库：Redis
2. 列式数据库：HBase, ClickHouse，适用于联机分析处理（OLAP）场景，数仓等
3. 文档数据库：MongoDB, ElasticSearch
4. 图数据库

Clickhouse：

1. 不支持事务：因为面向列。不存在隔离级别。ClickHouse的定位是分析性数据库，而不是严格的关系型数据库
2. 不支持高并发
3. 可处理大量读请求，数据压缩的特性



## 分布式理论

### 两阶段协议

https://juejin.cn/post/6844903621495095309


### 三阶段协议

https://juejin.cn/post/6844903621495111688

### FLP 不可能性原理

在网络可靠，存在节点失效（即便只有一个）的最小化异步模型系统中，不存在一个可以解决一致性问题的确定性算法。对于允许节点失效情况下，纯粹异步系统无法确保一致性在有限时间内完成。

举例：三个人在不同房间，进行投票（投票结果是 0 或者 1）。三个人彼此可以通过电话进行沟通，但经常会有人时不时地睡着。比如某个时候，A 投票 0，B 投票 1，C 收到了两人的投票，然后 C 睡着了。A 和 B 则永远无法在有限时间内获知最终的结果。

### CAP 定理

分布式系统只能交付以下三个所需特性中的两个特性：一致性、可用性和分区容错性。
- C（Consistency）：一致性，指的是所有客户端可以同时看到相同的数据。每当数据写入一个节点时，就必须立即将其转发或复制到系统中的所有其他节点
- A（Availability）：可用性，指的是可用性表示发出数据请求的任何客户端都会得到响应，即使一个或多个节点宕机。
- P（Partition tolerance）：分区容错性，指分区之间的通信可能失败。

一般来说，在分布式系统中不可避免会出现分区，因此认为 P 总是成立。因此若要实现一致性（CP），那么当出现分区问题时，就需要舍弃不一致的节点，即牺牲可用性。同理，若要实现可用性（AP），那么当出现分区问题时，就无法保证一致性。

Nacos集群默认支持 AP 原则（即不支持数据一致性，支持服务注册的临时实例），但也可切换至基于 Raft的 CP 原则（支持服务注册的永久实例）。

临时实例和持久化实例的区别：
- 持久化实例健康检查后会被标记为不健康，而临时实例会直接从列表中删除。

MongoDB 是一种 CP 数据存储——它通过牺牲可用性、保持一致性来解决网络分区问题。

### BASE 理论

- Basically Available(基本可用)，假设系统出现了不可预知的故障，但还是能用。
- Soft State（软状态），允许系统在多个不同节点的数据副本存在数据延时。
- Eventually Consistent（最终一致性），在软状态的一定期限过后，应达到数据的最终一致性。

核心思想是：即使无法做到强一致性（Strong consistency），但每个应用都可以根据自身的业务特点，采用适当的方式来使系统达到最终一致性（Eventual consistency）。



### Raft 算法

[可视化链接](https://thesecretlivesofdata.com/raft/)

三种角色：
1. Leader，发送心跳包以维护自己的Leader状态
2. Follower，响应Leader发送的心跳包
3. Candidate，如果没有接收到Leader的心跳信号后，Follower会变成Candidate并向其他节点发送拉票信息


记时器：
1. 选举超时时间（Follower拥有）：若超时前没有收到心跳包，认为Leader下线，此时Follower会变成Candidate；但反之如果接收到心跳包，则重置计时器。
2. 投票超时时间（Candidate拥有）：若超时前没有得到半数以上的票数，则竞选失败；反之成功。
3. 竞选等待超时时间（竞选失败的Follower拥有）：竞选失败的Follower需要等待一段时间后才能重新竞选。

### 脑裂

由于某种原因（网络不稳定）使得主从集群一分为二，导致master或者leader节点的数量不为1（0或2）。

- redis脑裂

master 机器突然脱离网络，使得 sentinel 集群无法感知到 master 的存在，会重新选举一个 master 节点。网络恢复后则会存在两个 master 节点。

- zookeeper脑裂

脑裂可能出现平票的情况，从而无法选举出 leader。

zk 中建议节点数是奇数。

# K8s

Kubernetes主要由以下几个核心组件组成：

- etcd保存了整个集群的状态；
- apiserver提供了资源操作的唯一入口，并提供认证、授权、访问控制、API注册和发现等机制；
- controller manager负责维护集群的状态，比如故障检测、自动扩展、滚动更新等；
- scheduler负责资源的调度，按照预定的调度策略将Pod调度到相应的机器上；
- kubelet负责维持容器的生命周期，同时也负责Volume（CVI）和网络（CNI）的管理；
- Container runtime负责镜像管理以及Pod和容器的真正运行（CRI）；
- kube-proxy负责为Service提供cluster内部的服务发现和负载均衡；

除了核心组件，还有一些推荐的add-ons（扩展）：

- kube-dns负责为整个集群提供DNS服务
- Ingress Controller为服务提供外网入口
- Heapster提供资源监控
- Dashboard提供GUI
- Federation提供跨可用区的集群
- Fluentd-elasticsearch提供集群日志采集、存储与查询

# 双重校验锁实现单例模式

```java
public class Singleton {

    private static volatile Singleton singleton = null; // volatile 必不可少，防止jvm指令重排优化

    private Singleton() {
    }

    public static Singleton getInstance(){
        //第一次校验singleton是否为空，为了提高代码执行效率，由于单例模式只要一次创建实例即可，所以当创建了一个实例之后，再次调用getInstance方法就不必要进入同步代码块，不用竞争锁。直接返回前面创建的实例即可。
        if(singleton==null){
            synchronized (Singleton.class){
                //第二次校验singleton是否为空，防止二次创建实例
                if(singleton==null){
                    singleton = new Singleton();
                }
            }
        }
        return singleton;
    }

    public static void main(String[] args) {
        for (int i = 0; i < 100; i++) {
            new Thread(new Runnable() {
                public void run() {
                    System.out.println(Thread.currentThread().getName()+" : "+Singleton.getInstance().hashCode());
                }
            }).start();
        }
    }
}
```

# 23种设计模式与六大原则


## 23种设计模式

- 创建型
    1. 单例模式（Singleton）
        - 饿汉模式：类加载的时候就创建实例，整个程序周期都存在
        - 懒汉模式：只有当第一次使用的时候才创建实例，线程不安全
        - 双重校验锁：
        - 静态内部类：
    2. 原型模式（Prototype），能够复制已有对象，而又无需使代码依赖它们所属的类。例如 `Cloneable` 接口是立即可用的原型模式。 
    3. [工厂方法模式（Factory Method）](https://refactoringguru.cn/design-patterns/factory-method/java/example)，在父类中提供一个创建对象的方法， 允许子类决定实例化对象
    4. [抽象工厂模式（Abstract Factory）](https://refactoringguru.cn/design-patterns/abstract-factory/java/example)，定义了用于创建不同产品的**接口**， 但将实际的创建工作留给了具体工厂类。 每个工厂类型都对应一个特定的产品变体。
    5. [建造者模式（Builder）](https://refactoringguru.cn/design-patterns/builder/java/example)，分步骤创建复杂对象。
- 行为型
    1. 模板方法模式（Template Method），它在基类中定义了一个算法的框架，允许子类在不修改结构的情况下重写算法的特定步骤。
    2. 责任链模式（Chain of Responsibility）
    3. 命令模式（Command）
    4. 迭代器模式（Iterator）
    5. 中介者模式（Mediator）
    6. 备忘录模式（Memeoto）
    7. 观察者模式（Observer）
    8. 状态模式（State）
    9. 策略模式（Strategy）
    10. 访问者模式（Visitor）
    11. 解释器模式（Interpreter）
- 结构型
    1. 适配器模式（Adaptor）
    2. 桥接模式（Bridge）
    3. 组合模式（Composite）
    4. 装饰模式（Decorator）
    5. 外观模式（Facade），为复杂系统、程序库或框架提供一个简单的接口。通常作用于整个对象子系统上。
    6. 享元模式（Flyweight），共享多个对象的部分状态将内存消耗最小化。 
    7. 代理模式（Proxy）

## 六大原则

1. 开闭原则，对扩展开放，对修改关闭。
2. 单一职责原则，顾名思义，一个类的职责只有有一个。
3. 里氏替换原则，子类可以扩展父类的功能，但不能改变父类原有的功能。
4. 依赖倒转原则，依赖于抽象，不能依赖于具体实现（面向接口编程）。
5. 接口隔离原则，类之间的依赖关系应该建立在最小的接口上。
6. 迪米特原则，一个软件实体应当尽可能少的与其他实体发生相互作用。

## 协程

简单理解：用户态的线程，上下文切换通过调用方去控制，而不是操作系统。

好处：减少了线程的重复高频创建；尽量避免线程的阻塞；

## 分布式事务 Seata

分两阶段提交

1. TC (Transaction Coordinator) - 事务协调者：维护全局和分支事务的状态，驱动全局事务提交或回滚。
2. TM (Transaction Manager) - 事务管理器：定义全局事务的范围：开始全局事务、提交或回滚全局事务。
3. RM (Resource Manager) - 资源管理器：管理分支事务处理的资源，与TC交谈以注册分支事务和报告分支事务的状态，并驱动分支事务提交或回滚。

执行流程：
1. TM 向 TC 请求发起(Begin)、提交(Commit)、回滚(Rollback)等全局事务。
2. TM 把代表全局事务的XID绑定到分支事务上。
3. RM 向 TC 注册，把分支事务关联到XID代表的全局事务中。
4. RM 把分支事务的执行结果上报给 TC。
5. TC 发送分支提交（Branch Commit）或分支回滚（Branch Rollback）命令给RM。

[四大模式](https://www.51cto.com/article/713007.html)：
1. AT模式，能适用于大部分事务情况。
2. XA模式
3. SAGA模式，核心思想是将长事务拆分成多个本地短事务。
4. TCC模式，核心思想是针对每个操作都要注册一个与其对应的确认和补偿操作。

其他分布式事务解决方案：
1. 基于 RocketMQ 的消息事务
2. 二阶段提交
3. 三阶段提交，CanCommit、PreCommit、DoCommit三阶段

## ZooKeeper（CP）

使用 ZAB 协议，包括两种运行模式：
1. 消息广播（Leader 正常运行），所有事务请求由单一主进程处理，Leader 转换为事务 Proposal 并广播分发给 Follower，Leader 等待 Follower 的反馈，超过半数的 Follower 反馈消息后，Leader 再发送 Commit 消息提交事务 Proposal。

2. 崩溃恢复（Leader 不可用时），新选举产生的 Leader 与过半的 Follower 进行同步，使数据一致，同步后进入消息广播模式。

主要服务于分布式系统，可以用ZooKeeper来做：统一配置管理、统一命名服务、[分布式锁](https://blog.csdn.net/weixin_40149557/article/details/117268491)、集群管理，用来解决分布式集群中应用系统的一致性问题，构建 ZooKeeper 集群时使用的服务器最好是奇数台。其设计目标是将那些复杂且容易出错的分布式一致性服务封装起来，构成一个高效可靠的原语集，并以一系列简单易用的接口提供给用户使用。

数据结构：Znode节点，与Unix文件系统类似，通过路径来标识，例如 `/home/app`，Znode节点又分为两种类型：临时节点、持久节点、临时顺序节点、持久顺序节点。客户端和服务端断开连接后，临时节点会自动删除但持久节点不会。[分布式锁使用的是临时节点](https://www.51cto.com/article/687086.html)。

Watcher 监听器：节点的数据发生变化后会通知到节点的监听器。

基本命令：
- create : 在树中的某个位置创建一个节点
- delete : 删除一个节点存在：测试节点是否存在于某个位置
- get data : 从节点读取数据
- set data： 将数据写入节点
- get children : 检索节点的子节点列表
- sync : 等待数据被传播

实现分布式锁原理：判断能否创建临时节点，如果不能则监听父节点（非公平锁）或上一个节点（公平锁），任务执行完成后释放该节点并通知所有监听的节点。

实现注册中心原理：初始化时 Provider 先向目录写入 URL 地址，Consumer 订阅相同目录的 URL 地址和自己的 URL 地址，监控中心订阅 Provider 和 Consumer 的 URL 地址；Consumer 在第一次调用服务时，会通过注册中心找到相应的服务的IP地址列表，并缓存到本地，以供后续使用；当 Provider 下线时，会在列表中移除 URL 并将新的 URL 地址发送给 Consumer 并缓存至本地，服务上线也是一样的。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/76e2b26a0fdd40ff9bc96837b4865dba~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp)

与 Nacos 的区别：

Nacos 是 AP 的，保证可用性，每个节点都是平等的，若干个节点 crash 后不会影响正常节点，但查到的信息不一定是最新的。ZK 在 crash 后进行 leader 选举，期间是不可用的。


## 微服务框架：Dubbo

底层基于 Netty 的 NIO 框架，基于 TCP 协议传输

### 四种负载均衡策略

支持服务端服务级别、服务端方法级别、客户端服务级别、客户端方法级别的负载均衡配置。还可以拓展负载均衡算法。

1. 随机负载均衡，**默认**策略
2. 轮询负载均衡
3. 最少活跃调用数，每个 Provider 都有一个计数器，开始调用则计数器加一，结束调用计数器减一。原则是将请求分配给处理速度快的 Provider。
4. 一致性哈希负载均衡

### HTTPS 如何保证可靠传输

TLS 协议：
1. 对称加密/非对称加密
2. 数字证书
3. 三次握手/四次挥手

## 进程和线程

区别：
1. 进程是系统资源分配的最小单位，实现了操作系统的并发；线程是CPU调度的最小单位，实现了进程内部的并发。
2. 进程在执行过程中拥有独立的内存单元，而多个线程共享进程的内存。
3. 进程切换的开销远大于线程切换的开销。
4. 进程之间不会相互影响，但线程挂掉会影响整个进程。

进程间通信方式：
1. 管道
2. 信号量
3. 消息队列
4. 共享内存
5. 套接字，也可以用于不同主机之间进程的通信

线程间通信方式：
1. 临界区
2. 互斥量（Synchronized/Lock）
3. 信号量（Semphore）
4. 事件（Notify/Wait）

## 用户态和内核态

为什么要有这两个状态？保护机制，防止用户误操作或恶意破坏系统

### 用户态
1. 是用户进程/线程所在的区域，主要用于执行用户程序
2. 运行的代码会受到CPU的很多检查，不能直接访问内核数据
3. 只拥有受限的权限
4. 只能响应部分中断请求
5. 只能访问受限的地址空间

### 内核态
1. 是内核进程/线程所在的区域，主要用于执行操作系统程序，硬件交互
2. 运行的代码不受任何限制，可以执行任意指令
3. 拥有最高权限
4. 可以响应所有中断请求
5. 可以访问所有内存空间


### 用户态切换到内核态

1. 系统调用（主动）。操作系统在执行用户程序的时候主要工作在用户态，当执行没有权限的任务时，才切换到内核态。
2. 异常（被动）。执行用户程序出现异常时，会从用户态切换到内核态处理异常
3. 外围设备中断（被动）。中断发生时，如果中断之前在运行用户态的程序，那么会切换至内核态处理中断。


## 服务注册与发现

如果自己实现服务注册与发现，需要考虑以下三点：
1. Register, 服务启动时候进行注册
2. Query, 查询已注册服务信息
3. Healthy Check, 确认服务状态是否健康

实现方案：
1. Eureka，AP
2. ZooKeeper，CP，Paxos 算法
3. Consul，CP，Raft 算法
4. Etcd，CP，Raft 算法

Paxos 和 Raft 算法都属于一致性算法，所以是保证 CP

### 两种模式
1. 客户端发现模式，首先要进行的是到服务注册中心获取服务列表，然后再根据调用端本地的负载均衡策略，进行服务调用。
2. 服务端发现模式，调用方直接向服务注册中心进行请求，服务注册中心再通过自身负载均衡策略，对微服务进行调用。这个模式下，调用方不需要在自身节点维护服务发现逻辑以及服务注册信息。


