---
title: SpringBoot开发二十-Redis入门以及Spring整合Redis
date: 2019-08-30 11:29:24
tags: SpringBoot
categories: SpringBoot项目
---

# 需求介绍

安装 Redis，熟悉 Redis 的命令以及整合Redis，在Spring 中使用Redis。

# 代码实现

Redis 内置了 16 个库，索引是 0-15 ，默认选择第 0 个

Redis 的常用命令：

```java
// 切换到第 1 个库
select 1 
// 刷新一个库
flushdb
// 查看这个数据库里面有哪些key
Keys *

String 类型数据的操作:
// 设置key的值
set k1 1
// 获得key的值
get k1
// 删除key的值
del k1
// 对key值自增自减
incr k1
decr k1

hash 类型数据的操作:
// 设置key的值
格式是：hset hash的key 项的key 项的值
例如：hset myhash id 1
// 设置多对key值
格式是：hmset hash的key 项的key 项的值
例如：hmset myhash id 1 name xixi
// 获取key的值
格式是：hget hash的key 项的key
例如：hget myhash id
// 获取多对key的值
格式是：hmget hash的key 项的key
例如：hmget myhash id name
// 获取该key下所有的值
格式是：hgetall hash的key
例如：hgetall myhash
// 如果项不存在则赋值，存在时什么都不做
格式是：hsetnx Hash的key 项的key 项的值
例如：hsetnx myhash address earth
// 判断键值是否存在
格式是：hexists hash的key 项的key
例如：hexists myhash id
```

Spring 引入 Redis

首先要在 pom.xml 引入依赖

```
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

然后需要对 Redis 进行配置，要在配置文件 application.properties 里面配置数据库的参数。

```java
# RedisProperties
spring.redis.database=11
spring.redis.host=localhost
spring.redis.port=6379
```

然后要编写配置类来构造RedisTemplate。其实呢SpringBoot 也对Redis进行了配置RedisTemplate，但是由于Redis是一个key-value的数据库，它在做的时候把key配置成了Object类型，但是由于我们一般都用String，所以重新配置一下。

```java
package com.nowcoder.community.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.RedisSerializer;

@Configuration
public class RedisConfig {
    // 我们要通过 Template 访问数据库，那么它要想具备访问数据库的能力就要能创建连接，而连接又是由连接工厂创建的，所以要把连接工厂注入进来
    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        // 使得Template有访问数据库的能力
        template.setConnectionFactory(redisConnectionFactory);

        // 设置key的序列化的方式
        template.setKeySerializer(RedisSerializer.string());
        // 设置value的序列化方式
        template.setValueSerializer(RedisSerializer.json());
        // 设置hash的key的序列化的方式
        template.setHashKeySerializer(RedisSerializer.string());
        // 设置hash的value的序列化的方式
        template.setHashValueSerializer(RedisSerializer.json());

        template.afterPropertiesSet();
        return template;
    }
}
```

然后访问数据的时候就是以下用法：

```java
redisTemplate.opsForValue(); // 访问String
redisTemplate.opsForHash(); // 访问Hash
redisTemplate.opsForList(); // 访问List
redisTemplate.opsForSet(); // 访问Set
redisTemplate.opsForZSet(); // 访问ZSet
```

现在就可以通过 RedisTemplate 来访问Redis数据库，来添加删除数据。

```java
package com.nowcoder.community;

import org.aspectj.lang.annotation.Aspect;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.dao.DataAccessException;
import org.springframework.data.redis.core.BoundValueOperations;
import org.springframework.data.redis.core.RedisOperations;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.core.SessionCallback;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringRunner;

import java.util.concurrent.TimeUnit;

@RunWith(SpringRunner.class)
@SpringBootTest
@ContextConfiguration(classes = CommunityApplication.class)
public class RedisTests {
    @Autowired
    private RedisTemplate redisTemplate;

    @Test
    public void testStrings() {
        String redisKey = "test:count";
        redisTemplate.opsForValue().set(redisKey, 1);
        System.out.println(redisTemplate.opsForValue().get(redisKey));
        System.out.println(redisTemplate.opsForValue().increment(redisKey));
        System.out.println(redisTemplate.opsForValue().decrement(redisKey));
    }

    @Test
    public void testHashes() {
        String redisKey = "test:user";

        redisTemplate.opsForHash().put(redisKey, "id", 1);
        redisTemplate.opsForHash().put(redisKey, "username", "zhangsan");

        System.out.println(redisTemplate.opsForHash().get(redisKey, "id"));
        System.out.println(redisTemplate.opsForHash().get(redisKey, "username"));
    }

    @Test
    public void testLists() {
        String redisKey = "test:ids";

        redisTemplate.opsForList().leftPush(redisKey, 101);
        redisTemplate.opsForList().leftPush(redisKey, 102);
        redisTemplate.opsForList().leftPush(redisKey, 103);

        System.out.println(redisTemplate.opsForList().size(redisKey));
        System.out.println(redisTemplate.opsForList().index(redisKey, 0));
        System.out.println(redisTemplate.opsForList().range(redisKey, 0, 2));

        // 从左边弹出,左边leftpop,右边rightpop
        System.out.println(redisTemplate.opsForList().leftPop(redisKey));
        System.out.println(redisTemplate.opsForList().leftPop(redisKey));
        System.out.println(redisTemplate.opsForList().leftPop(redisKey));
    }

    @Test
    public void testSets() {
        String redisKey = "test:teachers";

        redisTemplate.opsForSet().add(redisKey, "刘备", "关羽", "张飞", "赵云", "诸葛亮");

        System.out.println(redisTemplate.opsForSet().size(redisKey));
        System.out.println(redisTemplate.opsForSet().pop(redisKey));
        System.out.println(redisTemplate.opsForSet().members(redisKey));
    }

    @Test
    public void testSortedSets() {
        String redisKey = "test:students";

        redisTemplate.opsForZSet().add(redisKey, "唐僧", 80);
        redisTemplate.opsForZSet().add(redisKey, "悟空", 90);
        redisTemplate.opsForZSet().add(redisKey, "八戒", 50);
        redisTemplate.opsForZSet().add(redisKey, "沙僧", 70);
        redisTemplate.opsForZSet().add(redisKey, "白龙马", 60);

        // 统计数据
        System.out.println(redisTemplate.opsForZSet().zCard(redisKey));
        // 查询某个人的分数
        System.out.println(redisTemplate.opsForZSet().score(redisKey, "八戒"));
        // 查询排名，默认从小到大
        System.out.println(redisTemplate.opsForZSet().reverseRank(redisKey, "八戒"));
        // 查询区间排名数据
        System.out.println(redisTemplate.opsForZSet().reverseRange(redisKey, 0, 2));
    }

    @Test
    public void testKeys() {
        redisTemplate.delete("test:user");

        System.out.println(redisTemplate.hasKey("test:user"));

        redisTemplate.expire("test:students", 10, TimeUnit.SECONDS);
    }

    // 多次访问同一个key
    @Test
    public void testBoundOperations() {
        String redisKey = "test:count";
        BoundValueOperations operations = redisTemplate.boundValueOps(redisKey);
        operations.increment();
        operations.increment();
        operations.increment();
        operations.increment();
        operations.increment();
        System.out.println(operations.get());
    }

    // Redis编程式事务，并不一定满足我们之前说过的事务，因为不是关系型数据库
    @Test
    public void testTransactional() {
        Object obj = redisTemplate.execute(new SessionCallback() {
            @Override
            public Object execute(RedisOperations operations) throws DataAccessException {
                String redisKey = "test:tx";
                // 启用事务
                operations.multi();

                operations.opsForSet().add(redisKey, "zhangsan");
                operations.opsForSet().add(redisKey, "lisi");
                operations.opsForSet().add(redisKey, "wangwu");

                System.out.println(operations.opsForSet().members(redisKey));
                // 提交事务
                return operations.exec();
            }
        });
        System.out.println(obj);
    }
}
```

