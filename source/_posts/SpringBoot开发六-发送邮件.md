---
title: SpringBoot开发六-发送邮件
date: 2019-08-11 13:48:27
tags: SpringBoot
categories: SpringBoot项目
---

# 需求介绍—发送邮件

首先要进行邮箱设置，要启用客户端`SMTP`服务。

而且`SpringBoot`也给了`JavaMailSender`发送邮件。

# 代码实现

首先你需要设置好邮箱，步骤百度一大堆，记住要配置一个授权码，是需要在后续进行配置的`password`。

然后就是正式的来写了。

首先引入一个`jar`包

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-mail</artifactId>
	<version>2.1.6.RELEASE</version>
</dependency>
```

然后在`application.properties`进行邮箱的配置

```
# MailProperties
spring.mail.host=smtp.163.com
spring.mail.port=465
spring.mail.username=***@163.com
spring.mail.password=***
spring.mail.protocol=smtps
spring.mail.properties.smtp.auth=true
spring.mail.properties.mail.smtp.ssl.enable=true
```

那么这个邮箱发送的话我们就建一个工具类`MailClient`，实现能够复用。

```java
package com.nowcoder.community.util;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.mail.javamail.JavaMailSender;
import org.springframework.mail.javamail.MimeMessageHelper;
import org.springframework.stereotype.Component;

import javax.mail.MessagingException;
import javax.mail.internet.MimeMessage;

@Component
public class MailClient {
    private static final Logger logger = LoggerFactory.getLogger(MailClient.class);

    @Autowired
    private JavaMailSender mailSender;
	// 每次发送的username，也就是我们之前配置的名称
    @Value("${spring.mail.username}")
    private String from;
	// 实现一个方法调用，需要告诉方法你发给谁，主题是什么，内容是什么
    // 所以主要是要构建MimeMessage，在SpringBoot中给我们MimeMessageHelper这边帮助类帮助我们进行构建这个对象
    public void sendMail(String to, String subject, String content) {
        try {
            MimeMessage message = mailSender.createMimeMessage();
            MimeMessageHelper helper = new MimeMessageHelper(message);
            helper.setFrom(from);
            helper.setTo(to);
            helper.setSubject(subject);
            // 加了true，支持发送html邮件
            helper.setText(content, true);
            mailSender.send(helper.getMimeMessage());
        } catch (MessagingException e) {
            logger.error("发送邮件失败:" + e.getMessage());
        }
    }
}
```

那么我们写一个测试类测试一下`MailTests`：

```java
package com.nowcoder.community;

import com.nowcoder.community.util.MailClient;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringRunner;
import org.thymeleaf.TemplateEngine;
import org.thymeleaf.context.Context;


@RunWith(SpringRunner.class)
@SpringBootTest
@ContextConfiguration(classes = CommunityApplication.class)
public class MailTests {
    @Autowired
    private MailClient mailClient;
	// 主动的调用thymeleaf模板引擎
    @Autowired
    private TemplateEngine templateEngine;

    @Test
    public void testTextMail() {
        mailClient.sendMail("838567391@qq.com","test","Welcome");
    }

    // 你发送html模板的话，需要利用thymeleaf模板，我在下面给了一个demo，
    @Test
    public void testHtmlMail() {
        Context context = new Context();
        // 给模板传参数，要和demo里面申明的一样
        context.setVariable("username","sunday");
        // 得到内容，把context传到demo里面
        String content = templateEngine.process("/mail/demo",context);
        System.out.println(content);
        mailClient.sendMail("838567391@qq.com","HtmlTest",content);
    }
}
```

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>邮件实例</title>
</head>
<body>
    <p>欢迎你，<span style="color:red;" th:text="${username}"></span>!</p>
</body>
</html>
```

这样的话就能进行邮件发送了，如果你发给`qq`邮箱的话要注意邮件一般是发送到了垃圾箱里面。