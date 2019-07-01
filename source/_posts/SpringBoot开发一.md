---
title: SpringBoot开发一
date: 2019-06-29 16:57:02
tags: SpringBoot
categories: SpringBoot项目
---

# 项目介绍

牛客高级项目课，主要是完成牛客网的讨论社区的搭建。

涉及到的技术架构：

`Spring`，`SpringBoot`，`SpringMVC`，`MyBatis`，`Redis`，`Kafka`（消息队列服务器），`Elasticsearch`（搜索引擎），`SpringSecurity`（管理系统的权限），`SpringActuator`（对系统进行全面的监控）。

 # 创建项目流程

主要还是利用 `Spring Initializr`来帮助我们创建，它的底层还是应用了Maven来帮我们管理jar包，通常我们会需要这些：`AOP`，`WEB`，`DevTools`，`thymeleaf`。现在它上面已经不能直接引入AOP了，我们需要手动引入即可。创建好项目，来写一个简单的`Hello SpringBoot!`页面。

# 代码

创建一个controller包，在包里面写页面入口。

```java
package com.nowcoder.community.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class AlphaController {
    @RequestMapping("/hello")
    @ResponseBody
    public String sayHello() {
        return "Hello Spring Boot!";
    }
}
```

这个时候在浏览器输入：

```
http://localhost:8080/hello
```

就可以看到：

![](1.png)

注意默认的端口是：8080，但是有时候这个端口可能被别的软件占用所以呢可以在`application.properties`这个文件里进行配置。

```java
server.port=8080
server.servlet.context-path=/community
```

可以设置端口和页面前缀这些东西，加上后：

![](2.png)