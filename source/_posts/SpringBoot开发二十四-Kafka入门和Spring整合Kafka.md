---
title: SpringBoot开发二十四-Kafka入门和Spring整合Kafka
date: 2019-09-02 10:08:06
tags: SpringBoot
categories: SpringBoot项目
---

# 需求分析

阻塞队列（BlockingQueue）

解决线程通信的问题，阻塞方：put，take。

主要是生产者，消费者模式，生产者是产生数据的线程，消费者是使用数据的线程。

kafka：一个分布式的流媒体平台，主要应用在消息系统，日志收集，用户行为追踪，

它的特点：高吞吐量，消息持久化，高可靠性，高拓展性。

消息持久化能支撑 Kafka 的高吞吐，持久化就是把数据永久的保存到一个介质里比方说硬盘，也就是说 Kafka 会把数据内容存到硬盘里而不是放到内存里面，所以它能大量处理海量数据，而且顺序读写硬盘而不是随机读写硬盘性能是高于对内存的随机读取。

然后进 http://kafka.apache.org 进行下载。

然后进行对应的配置，具体的就百度就行。

使用命令行来使用 Kafka：

```Java
// 创建了一个topic为test，1个分区，1个副本
kafka-topics.bat --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic test

// 查看某个服务器上的所有的分区
kafka-topics.bat --list --bootstrap-server localhost:9092

// 生产者生产数据
kafka-console-produce.bat --broker-list localhost:9092 --topic test
Hello
World

// 再通过消费者看能不能读到这个信息
kafka-console-consumer.bat --broker-list localhost:9092 --topic test --from-beginning
```

现在就用 Spring 整合 Kafka。

先在 pom.xml 引入依赖：

```
<dependency>
	<groupId>org.springframework.kafka</groupId>
	<artifactId>spring-kafka</artifactId>
</dependency>
```

然后在 application.properties 里进行 Kafka 配置

```java
# KafkaProperties
spring.kafka.bootstrap-servers=localhost:9092
# 配置一下消费者分组ID
spring.kafka.consumer.group-id=test-consumer-group
# 是否自动提交消费者的偏移量
spring.kafka.consumer.enable-auto-commit=true
# 自动提交的频率
spring.kafka.consumer.auto-commit-interval=3000
```

现在写一段测试代码看怎么在 Spring 里面使用 Kafka：

```java
package com.nowcoder.community;

import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.kafka.annotation.KafkaListener;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.stereotype.Component;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringRunner;

@RunWith(SpringRunner.class)
@SpringBootTest
@ContextConfiguration(classes = CommunityApplication.class)
public class KafkaTests {
    @Autowired
    private KafkaProducer kafkaProducer;

    @Test
    public void testKafka() {
        kafkaProducer.sendMessage("test", "你好");
        kafkaProducer.sendMessage("test", "在吗");

        try {
            Thread.sleep(1000 * 10);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

}

@Component
class KafkaProducer {
    @Autowired
    private KafkaTemplate kafkaTemplate;

    public void sendMessage(String topic, String content) {
        kafkaTemplate.send(topic, content);
    }
}

@Component
class KafkaConsumer {
    // 被动的调用，申明监听的主题
    @KafkaListener(topics = {"test"})
    // 调用这个方法的时候会把这个数据进行封装成 ConsumerRecord 格式
    public void handleMessage(ConsumerRecord record) {
        System.out.println(record.value());
    }
}
```

