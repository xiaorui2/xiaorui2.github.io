---
title: SpringBoot开发二十五-发送系统通知和显示系统通知
date: 2019-09-02 10:08:38
tags: SpringBoot
categories: SpringBoot项目
---

# 需求分析

利用 Kafka 做消息队列实现发送系统通知，基于事件对代码进行封装

触发事件：

- 评论后，发布通知
- 点赞后，发布通知
- 关注后，发布通知

处理事件：

- 封装事件对象，对于事件发生的时候所需的数据进行封装，包含这条消息所含的所有的数据，
- 开发事件的生产者
- 开发事件的消费者

# 代码实现

首先我们封装一个事件对象 Event，包含事件触发的时候相关的一切的信息 

```java
package com.nowcoder.community.entity;

import java.util.HashMap;
import java.util.Map;

public class Event {

    // 事件的主题（类型）
    private String topic;
    // 事件触发的人
    private int userId;
    // 这个人发生的事件的实体类型是什么
    private int entityType;
    // 实体Id
    private int entityId;
    // 实体的作者
    private int entityUserId;
    // 处理其他的事件可能还要其他的数据需要记录，但是又没有办法预判，就把所有其他的数据存到map里面
    private Map<String, Object> data = new HashMap<>();

    public String getTopic() {
        return topic;
    }

    // 当我们在调用了这个方法的时候set了topic，那肯定我们还要set其他的数据，那么我们返回了当前对象我们就又能调用set方法set其他的数据，就比较方便。
    public Event setTopic(String topic) {
        this.topic = topic;
        return this;
    }

    public int getUserId() {
        return userId;
    }

    public Event setUserId(int userId) {
        this.userId = userId;
        return this;
    }

    public int getEntityType() {
        return entityType;
    }

    public Event setEntityType(int entityType) {
        this.entityType = entityType;
        return this;
    }

    public int getEntityId() {
        return entityId;
    }

    public Event setEntityId(int entityId) {
        this.entityId = entityId;
        return this;
    }

    public int getEntityUserId() {
        return entityUserId;
    }

    public Event setEntityUserId(int entityUserId) {
        this.entityUserId = entityUserId;
        return this;
    }

    public Map<String, Object> getData() {
        return data;
    }

    public Event setData(String key, Object value) {
        this.data.put(key, value);
        return this;
    }

}
```

有了事件对象，我们就来开发对应的生产者

```java
package com.nowcoder.community.event;

import com.alibaba.fastjson.JSONObject;
import com.nowcoder.community.entity.Event;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.stereotype.Component;

@Component
public class EventProducer {

    @Autowired
    private KafkaTemplate kafkaTemplate;

    // 处理事件
    public void fireEvent(Event event) {
        // 将事件发布到指定的主题
        kafkaTemplate.send(event.getTopic(), JSONObject.toJSONString(event));
    }
}
```

消费者：

首先我们在 CommunityConstant 定义一下主题的常量

```java
/**
* 主题: 评论
*/
String TOPIC_COMMENT = "comment";

/**
* 主题: 点赞
*/
String TOPIC_LIKE = "like";

/**
* 主题: 关注
*/
String TOPIC_FOLLOW = "follow";

/**
* 主题：发帖
*/
String TOPIC_PUBLISH = "publish";

/**
* 系统用户ID
*/
int SYSTEM_USER_ID = 1;
```

然后完成 EventConsumer

```java
package com.nowcoder.community.event;

import com.alibaba.fastjson.JSONObject;
import com.nowcoder.community.entity.DiscussPost;
import com.nowcoder.community.entity.Event;
import com.nowcoder.community.entity.Message;
import com.nowcoder.community.service.DiscussPostService;
import com.nowcoder.community.service.ElasticsearchService;
import com.nowcoder.community.service.MessageService;
import com.nowcoder.community.util.CommunityConstant;
import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.kafka.annotation.KafkaListener;
import org.springframework.stereotype.Component;

import java.util.Date;
import java.util.HashMap;
import java.util.Map;

@Component
public class EventConsumer implements CommunityConstant {

    private static final Logger logger = LoggerFactory.getLogger(EventConsumer.class);

    @Autowired
    private MessageService messageService;

    @Autowired
    private DiscussPostService discussPostService;

    @Autowired
    private ElasticsearchService elasticsearchService;

    @KafkaListener(topics = {TOPIC_COMMENT, TOPIC_LIKE, TOPIC_FOLLOW})
    public void handleCommentMessage(ConsumerRecord record) {
        if (record == null || record.value() == null) {
            logger.error("消息的内容为空!");
            return;
        }

        Event event = JSONObject.parseObject(record.value().toString(), Event.class);
        if (event == null) {
            logger.error("消息格式错误!");
            return;
        }

        // 发送站内通知
        Message message = new Message();
        message.setFromId(SYSTEM_USER_ID);
        message.setToId(event.getEntityUserId());
        message.setConversationId(event.getTopic());
        message.setCreateTime(new Date());

        Map<String, Object> content = new HashMap<>();
        content.put("userId", event.getUserId());
        content.put("entityType", event.getEntityType());
        content.put("entityId", event.getEntityId());

        if (!event.getData().isEmpty()) {
            for (Map.Entry<String, Object> entry : event.getData().entrySet()) {
                content.put(entry.getKey(), entry.getValue());
            }
        }

        message.setContent(JSONObject.toJSONString(content));
        messageService.addMessage(message);
    }

    // 消费发帖事件
    @KafkaListener(topics = {TOPIC_PUBLISH})
    public void handlePublishMessage(ConsumerRecord record) {
        if (record == null || record.value() == null) {
            logger.error("消息的内容为空!");
            return;
        }

        Event event = JSONObject.parseObject(record.value().toString(), Event.class);
        if (event == null) {
            logger.error("消息格式错误!");
            return;
        }

        DiscussPost post = discussPostService.findDiscussPostById(event.getEntityId());
        elasticsearchService.saveDiscussPost(post);
    }

}
```

