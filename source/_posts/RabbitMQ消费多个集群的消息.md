---
title: RabbitMQ消费多个集群的消息
date: 2023-09-01 15:39:20
categories:
- 技术
tags:
- java
- RabbitMQ
---

最近摸鱼成瘾，好久没写过技术相关的东西了...

昨天跟同事遇到了一个问题：

同事需要消费我的项目A发送的MQ消息和另一个同事的项目B发送的MQ消息，在测试环境下用的是同一条MQ集群，但是到了生产环境，就是两个不同的MQ集群了。RabbitMQ的自动配置类是单例模式，无法通过配置多个集群来达到效果，跟同事看了很久的RabbitMQ源码去思考，一开始我的想法是类似动态数据源，切面更换RabbitMQ配置，但是这仅适用于生产者，RabbitMQ的消费者是无法根据切面更换配置的。同事的想法是重写配置类，但是RabbitMQ的消费者配置类经过了很多层的封装，耦合得很厉害，也基本无法实现。

最后找到了一篇博客https://blog.csdn.net/b_ingrem/article/details/118969504其实思路跟同事是比较符合的，重写了两个`ConnectionFactory`，通过`@RabbitListener`中的`containerFactory`来指定使用的`ConnectionFactory`即可。

复制一下配置类代码方便以后使用。

```java
public class connectionFactory {

    /*
   定义连接，rabbitMQ
    */
    @Bean(name = "mainConnectionFactory")
    @Primary
    public ConnectionFactory mainConnectionFactory(
            @Value("${spring.rabbitmq.first.host}") String host,
            @Value("${spring.rabbitmq.first.port}") int port,
            @Value("${spring.rabbitmq.first.username}") String username,
            @Value("${spring.rabbitmq.first.password}") String password) {
        return connectionFactory(host, port, username, password);
    }

    /*
        定义连接
         */
    @Bean(name = "secondConnectionFactory")
    public ConnectionFactory secondConnectionFactory(
            @Value("${spring.rabbitmq.second.host}") String host,
            @Value("${spring.rabbitmq.second.port}") int port,
            @Value("${spring.rabbitmq.second.username}") String username,
            @Value("${spring.rabbitmq.second.password}") String password) {
        return connectionFactory(host, port, username, password);
    }

    /*
     定义连接
      */

    public CachingConnectionFactory connectionFactory(String host, int port, String username, String password) {
        CachingConnectionFactory connectionFactory = new CachingConnectionFactory();
        connectionFactory.setHost(host);
        connectionFactory.setPort(port);
        connectionFactory.setUsername(username);
        connectionFactory.setPassword(password);
        //  connectionFactory.setVirtualHost(virtual_host);
        return connectionFactory;
    }

    @Bean(name = "mainRabbitTemplate")
    @Primary
    public RabbitTemplate mainRabbitTemplate(@Qualifier("mainConnectionFactory") ConnectionFactory connectionFactory) {
        RabbitTemplate mainRabbitTemplate = new RabbitTemplate(connectionFactory);
        mainRabbitTemplate.setMessageConverter(new Jackson2JsonMessageConverter());
        return mainRabbitTemplate;
    }

    @Bean(name = "secondRabbitTemplate")
    public RabbitTemplate secondRabbitTemplate(@Qualifier("secondConnectionFactory") ConnectionFactory connectionFactory) {
        RabbitTemplate secondRabbitTemplate = new RabbitTemplate(connectionFactory);
        return secondRabbitTemplate;
    }



    @Bean(name = "mainFactory")
    @Primary
    public SimpleRabbitListenerContainerFactory myFactory(
            SimpleRabbitListenerContainerFactoryConfigurer configurer,
            @Qualifier("mainConnectionFactory") ConnectionFactory connectionFactory) {
        SimpleRabbitListenerContainerFactory factory = new SimpleRabbitListenerContainerFactory();
        factory.setMessageConverter(new Jackson2JsonMessageConverter());
        configurer.configure(factory, connectionFactory);
        return factory;
    }

    @Bean(name = "secondFactory")
    public SimpleRabbitListenerContainerFactory secondFactory(
            SimpleRabbitListenerContainerFactoryConfigurer configurer,
            @Qualifier("secondConnectionFactory") ConnectionFactory connectionFactory) {
        SimpleRabbitListenerContainerFactory factory = new SimpleRabbitListenerContainerFactory();
        factory.setConcurrentConsumers(10);
        factory.setMaxConcurrentConsumers(20);
        factory.setPrefetchCount(1);
        configurer.configure(factory, connectionFactory);
        return factory;
    }

}
```
