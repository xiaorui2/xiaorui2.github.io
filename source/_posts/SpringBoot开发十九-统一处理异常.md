---
title: SpringBoot开发十九-统一处理异常
date: 2019-08-30 11:28:47
tags: SpringBoot
categories: SpringBoot项目
---

# 需求介绍

首先服务端分为三层：表现层，业务层，数据层。

请求过来先到表现层，表现层调用业务层，然后业务层调用数据层。

那么数据层出现异常它会抛出异常，那异常肯定是抛给调用者也就是业务层，那么业务层会再抛给表现层，所以无论是哪个层次的异常最终都会汇总到表现层。

SpringBoot 给的解决方案是在项目的某一个特定的路径下（templates 目录下 error 下放 404.html 以及 500.html）加上对应特定错误状态的页面（一定是错误状态码的文件名）那么在发生错误的时候就会跳转到该页面。

# 代码

首先看下 SpringBoot 给的解决方案是在项目的某一个特定的路径下（templates 目录下 error 下放 404.html 以及 500.html）加上对应特定错误状态的页面（一定是错误状态码的文件名）那么在发生错误的时候就会跳转到该页面。

这样的话其实就已经生效了。

首先在 HomeController 里加一个请求，因为服务器发生异常之后，我们统一处理记录日志之后我们要重定向到这个 500 或者 404 的页面。

```java
@RequestMapping(path = "/error", method = RequestMethod.GET)
public String getErrorPage() {
        return "/error/500";
}

@RequestMapping(path = "/denied", method = RequestMethod.GET)
public String getDeniedPage() {
        return "/error/404";
}
```

然后利用 @ControllerAdvice 声明一个全局配置类 ExceptionAdvice，对所有的异常统一的处理

```java
package com.nowcoder.community.controller.advice;

import com.nowcoder.community.util.CommunityUtil;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

// 这个注解表示只扫描带有 @Controller 注解的bean
@ControllerAdvice(annotations = Controller.class)
public class ExceptionAdvice {

    private static final Logger logger = LoggerFactory.getLogger(ExceptionAdvice.class);

    @ExceptionHandler({Exception.class})
    public void handleException(Exception e, HttpServletRequest request, HttpServletResponse response) throws IOException {
        logger.error("服务器发生异常: " + e.getMessage());
        // 把异常非常详细的栈的信息都记录下来
        for (StackTraceElement element : e.getStackTrace()) {
            logger.error(element.toString());
        }
		
        // 记录好信息要给浏览器一个相应，重定向到 500 这个错误页面，但是注意要处理是异步请求返回 JSON字符串还是返回一个网页
        String xRequestedWith = request.getHeader("x-requested-with");
        // 异步请求的处理，相应一个字符串
        if ("XMLHttpRequest".equals(xRequestedWith)) {
            response.setContentType("application/plain;charset=utf-8");
            PrintWriter writer = response.getWriter();
            writer.write(CommunityUtil.getJSONString(1, "服务器异常!"));
        } else {
            response.sendRedirect(request.getContextPath() + "/error");
        }
    }

}
```

这样的话就解决了统一处理异常的需求。