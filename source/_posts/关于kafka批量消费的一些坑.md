---
title: 关于Kafka批量消费的一些坑
date: 2023-08-31 09:20:58
categories:
- 技术
tags:
- Kafka
- java
---

​	很久很久之前，发现Kafka的一些Topic发生了消费延迟，为了提高消费速率，我自作聪明地使用`containerFactory`并指定了一个批量消费的Factory：

```java
@Bean(value = "kafkaListenerContainerBatchFactory")
public KafkaListenerContainerFactory<ConcurrentMessageListenerContainer<String, String>> kafkaListenerContainerFactory() {
    ConcurrentKafkaListenerContainerFactory<String, String> factory = new ConcurrentKafkaListenerContainerFactory<>();
    factory.setConsumerFactory(consumerFactory());
    //消费者实例个数，并发消费（实例数*分布式机器数<=kafka分区数，避免浪费）
    factory.setConcurrency(concurrency);
    //支持并发消费
    factory.setBatchListener(true);
    //设置提交偏移量的方式, 默认是 batch
    factory.getContainerProperties().setAckMode(ContainerProperties.AckMode.MANUAL_IMMEDIATE);
    return factory;

}
```

​	曽以为这样就万事大吉，Kafka的消费延迟也确实消失了，直到昨天早上，生产报出问题，Kafka竟然出现了消息丢失的情况！用可视化工具检查了Kafka内的消息，确认并不是生产者的问题，那么消息丢失就是发生在消费者这了。

​	检查日志，发现一个很奇怪的问题：Kafka消费的offset有时候会开始中断几百条再消费。确认了这个group只有我一个项目在消费，所以不存在项目间抢夺消息的问题。

​	此时一个想法在我脑海浮现：难道是批量消费都没有执行完，就被强行中断了？因为这个group做了三个分区，并且开了三台机器来消费，所以每个机器的并发数都是1，也就是说，Kafka Consumer在每台机器上都是一个单线程。一开始怀疑的是`auto-offset-reset`这个配置项。在项目中，它的值是`latest`，官方文档的解释是

> earliest：自动将偏移量重置为最早的偏移量
> latest：自动将偏移量重置为最新偏移量

​	是不是在两次拉取中，因为前一次的消费速度太慢，导致前一次的offset并没有提交，后一次的消费则直接从最新的offset消费了？然而在本地的实验宣告该想法是不正确的，即使改为了`earliest`，依旧出现了offset跳跃的问题。那么只能确定，这个配置项只会在Kafka Consumer停止或重启时有影响。

​	那么我加快消费速度，让他能在两次拉取的间隔时间内全部消费完呢？我在本地开启一个线程池去并发消费，结果很明显，offset跳跃的情况消失了。根据日志可以看到，两次批量拉取的间隔时间是在3s左右。

​	为什么是3s？查了一圈发现最有可能的是`heartbeat.interval.ms`这个配置项在起作用。在Kafka中，除了消费者线程，还有一个心跳线程。生产者线程会定时发送心跳给心跳线程接收，如果心跳线程没有在一定时间内回应，生产者会默认该消费者已经挂了，将该消费者提出消费群组并进行重新的负载均衡（Rebalance）。但是如果只是消费速度慢，offset没有及时commit的话，就会出现以下情况：

> 生产者发送心跳 -> 消费者还在消费，offset未commit -> Kafka Consumer报出`CommitFailedException`，commit失败 -> 生产者未收到心跳回应，踢出该消费者并进行Rebalance -> 消费者并未挂掉，会重新订阅topic -> 消费者从最新的offset开始消费，发生offset跳跃

​	以上是我结合实际情况和文档、博客作出的一个较合理的猜测，真实情况还是得在实际项目中去继续观察......
