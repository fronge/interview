# MQ相关问题汇总
## 为什么用消息队列? 
  - 削峰
  - 解耦
  - 异步
## 消息队列优点缺点?
  - 可用性降低
    - 如果MQ崩掉，系统久挂了 
  - 系统的复杂性增加
    - MQ出现的故障造成系统故障
    - 消息堆积
    - 消息丢失
    - 一致性问题
  - 新技术引进带来新的问题
 
## kafka、activemq、rabbitmq、rocketmq都有什么优点和缺点？
  - activemq 以前用的比较多，现在用的少了，社区活跃度相对较低，不考虑使用
  - RabbitMQ 使用 erlang语言开发，但是有很好的社区活跃度，如果只是单纯的用，可以考虑，如果要深入的研究，包括做源码级别的改造，成本会比较大
  - RocketMQ 阿里开源的MQ系统，吞吐量高，可以使用，但是，因为是公司开源的，后续可能会出现维护问题，如果哪一天阿里把这个项目砍掉了，可能需要自己去维护
  - Kafka，大数据领域实时计算、日志采集等场景，用kafka，社区活跃度高

## MQ如何保证高可用?
  - RabbitMQ
    - 镜像集群模式  
  - Kafka
    - 分布式实现高可用架构 
    - 多broker(机器)，多分区
    - 每个broker 都存数据副本，保证数据的安全性
    - follower 机制，读写针对 leader, leader向follower副本同步数据，如果leader宕机，会选举出新的leader

## Kafka的topic、partition、消费者、消费组的概念以及关系
- 一个topic可以划分为多个partition，有助于提高消息处理的并行效率
- 一个消费者组内，一个消费者和一个partition的关系是1:N的关系
- 一个消费者可以消费一个或多个partition，在同一时刻，一个partition只能被一个消费者消费
- 消费者属于消费者组，多个消费者组成一个消费者组
- 消费组与消费组并行消费同一个topic, 互不影响

## Kafka如何保证顺序消费？
- 首先kafka消息是不存在顺序的，如果想要顺序消费，一个topic只建立一个partition,所有数据都发送到同一个partition中，保证数据顺序性
- 可以保证最终顺序性，在消费端组织数据，等所有数据消费到后进行排序处理
## Kafka如何保证消息不丢失?
- 开启ACK机制，手动ACK，保证消息发送成功

## Kafka如何保证数据不重复?
- 当开启ACK机制后，可能会出现消息重复发送到场景，这时候，可以在消费端做去重，具体做法，每条消息定义一个唯一字段，在消费端代码层面处理重复数据，保证消费到的数据为非重复数据

## kafka rebalence 重平衡
- [参考博客](https://blog.csdn.net/qq_35901141/article/details/115710558)
- rebalance概览
  - rebalance中文含义为再平衡。它本质上是一组协议，它规定了一个 consumer group 是如何达成一致来分配订阅 topic 的所有分区的。
- 触发条件
  - consumer group成员发生变更，比方说有新的consumer实例加入，或者有consumer实例离开组，或者有consumer实例发生奔溃。
  - consumer group订阅的topic数发生变更，这种情况主要发生在基于正则表达式订阅topic情况，当有新匹配的topic创建时则会触发rebalance。
  - consumer group 订阅的topic分区数发生变更
- rebalance分区分配策略
  - 分区分配策略决定了将topic中partition分配给consumer group中consumer实例的方式
  - 三种rebalance分区分配策略
    - range
    - round-robin
    - sticky
- rebalance协议
  - JoinGroup请求：consumer请求加入组
  - SyncGroup请求：group leader把分配方案同步更新到组内所有成员中
  - Heartbeat请求：consumer定期向coordinator汇报心跳表明自己依然存活。
  - LeaveGroup请求：consumer主动通知coordinator自己将要离开consumer group
  - DescribeGroup请求：查看组的所有的所有信息，包括成员信息、协议信息、分配方案、以及订阅信息等。该请求主要供管理员使用，coordinator不使用该请求实现rebalance

## Kafka原理
- [参考博客](https://www.cnblogs.com/dreamroute/p/13092117.html)

## Kafka的集群

