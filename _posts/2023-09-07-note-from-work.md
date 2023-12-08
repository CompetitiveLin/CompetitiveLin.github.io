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

4. Seata，用于微服务的分布式事务框架，也有两阶段提交

5. 限流算法：漏桶和令牌桶算法，漏桶算法处理请求的速度固定，突发请求过多时会丢弃；令牌桶算法除了限制数据的平均传输速率外，还要求允许某种程度的突发传输。

6. [常见 HTTP 状态码](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status)：2XX，成功响应；3XX，重定向消息；4XX，客户端错误响应；5XX，服务端错误响应。

7, 请求分为四部分：请求行，请求头，空行，请求正文（不一定有）。

## 常见NoSQL数据库类型
1. 键值数据库：Redis
2. 列式数据库：HBase, ClickHouse，适用于联机分析处理（OLAP）场景
3. 文档数据库：MongoDB, ElasticSearch
4. 图数据库

Clickhouse：

1. 不支持事务：因为面向列。不存在隔离级别。ClickHouse的定位是分析性数据库，而不是严格的关系型数据库
2. 不支持高并发
3. 可处理大量读请求，数据压缩的特性


## ACID 原则
- Atomicity（原子性），每次事务是原子的，事务包含的所有操作要么全部成功，要么全部不执行。一旦有操作失败，则需要回退状态到执行事务之前；
- Consistency（一致性），数据库的状态在事务执行前后的状态是一致的和完整的，无中间状态。即只能处于成功事务提交后的状态；
- Isolation（隔离性），各种事务可以并发执行，但彼此之间互相不影响。按照标准 SQL 规范，从弱到强可以分为未授权读取、授权读取、可重复读取和串行化四种隔离等级；
- Durability（持久性），状态的改变是持久的，不会失效。一旦某个事务提交，则它造成的状态变更就是永久性的。


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