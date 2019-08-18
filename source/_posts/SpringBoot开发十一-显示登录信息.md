---
title: SpringBoot开发十一-显示登录信息
date: 2019-08-13 21:44:04
tags: SpringBoot
categories: SpringBoot项目
---

# 需求介绍—显示登录信息

我们需要在每个页面的头部都要把登录用户的头像显示出来，另外在详细信息里面你需要显示用户的名字，除此之外如果登录了，我们显示首页 信息 头像 三个功能的链接，否则显示首页 登录两个功能点，也就是根据登录与否显示头部的内容。

我们每个静态页面都有这个内容，都需要显示登录信息，那么开发这个功能你需要每个请求都需要实现这个工能。我们想要低耦合的解决这个功能，就利用 Spring 的拦截器，它可以拦截访问服务器的请求，拦截请求之后可以在拦截这个请求之后的开始或者结束的部分插入一些代码，从而解决一些请求共有点的一些功能实现。

# 代码实现

使用拦截器的方法：

先写一个类实现 HandlerInterceptor 接口，然后配置拦截器的处理范围，拦截哪些请求，不拦截哪些请求。写个实例，先写类 AlphaInterceptor 实现 HandlerInterceptor 接口：

```java
package com.nowcoder.community.controller.interceptor;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@Component// 把拦截器交给容器管理
public class AlphaInterceptor implements HandlerInterceptor {

    private static final Logger logger = LoggerFactory.getLogger(AlphaInterceptor.class);

    // 在Controller之前执行
    // Object是拦截的方法
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        logger.debug("preHandle: "+ handler.toString());
        return true;
    }

    // 在调用完Controller之后执行，模板之前执行
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        logger.debug("postHandle:" + handler.toString());
    }

    // 在TemplateEngine之后执行
    // 异常是在调用Controller或者模板过程中出现了异常就会赋值给这个ex参数
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        logger.debug("afterCompletion: " + handler.toString());
    }
}
```

我们写好了之后要写一个配置类 WebMvcConfig：

一般配置类都是引入第三方的 Bean，但是拦截器需要实现 WebMvcConfigurer 接口。

```java
package com.nowcoder.community.config;

import com.nowcoder.community.controller.interceptor.AlphaInterceptor;
import com.nowcoder.community.controller.interceptor.LoginRequiredInterceptor;
import com.nowcoder.community.controller.interceptor.LoginTicketInterceptor;
import com.nowcoder.community.controller.interceptor.MessageInterceptor;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class WebMvcConfig implements WebMvcConfigurer {
    @Autowired
    private AlphaInterceptor alphaInterceptor;
	// .excludePathPatterns 表示哪些路径不被拦截比方说一些静态资源，
    // .addPathPatterns 表示确定的拦截的路径
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(alphaInterceptor)
                .excludePathPatterns("/**/*.css", "/**/*.js", "/**/*.png", "/**/*.jpg", "/**/*.jpeg")
                .addPathPatterns("/register", "/login");
}
```

知道拦截器怎么用了，我们就正式来写一下，根据我们的需求，

我们需要在请求开始时查询登录用户，然后在本次请求中持有用户数据（就是存到内存里），然后在模板视图上引用，最后在请求结束时清理用户数据。

![](1.png)

第一步我们知道 Cookie 是从 HttpServletRequest 对象里面取出来的，因为很多情况下都需要获获取 Cookie，所以写一个工具帮我们实现一下 CookieUtil：

```java
package com.nowcoder.community.util;

import org.springframework.web.bind.annotation.CookieValue;

import javax.servlet.http.Cookie;
import javax.servlet.http.HttpServletRequest;

public class CookieUtil {

    public static String getValue(HttpServletRequest httpServletRequest, String name) {
        if (httpServletRequest == null || name == null) {
            throw new IllegalArgumentException("参数为空！");
        }

        Cookie[] cookies = httpServletRequest.getCookies();
        if (cookies != null) {
            for (Cookie cookie : cookies) {
                if (cookie.getName().equals(name)) {
                    return cookie.getValue();
                }
            }
        }
        return null;
    }
}
```

其次我们说了我们通过 Ticket 查询到的用户，我们需要把它存下来，但是由于服务器对应客户端是一对多的，它是一个处理多线程的，每个线程隔离的存这个对象，那么我们写个工具 HostHolder 存 User 对象，起到一个容器的作用：

```java
package com.nowcoder.community.util;

import com.nowcoder.community.entity.User;
import org.springframework.stereotype.Component;

/**
 * 持有用户信息，用于代替session对象
 */
@Component
public class HostHolder {
    private ThreadLocal<User> users = new ThreadLocal<>();

    public void setUser(User user) {
        users.set(user);
    }

    public User getUser() {
        return users.get();
    }

    public void clear() {
        users.remove();
    }
}
```

然后写个拦截器 LoginTicketInterceptor：

```java
package com.nowcoder.community.controller.interceptor;

import com.nowcoder.community.entity.LoginTicket;
import com.nowcoder.community.entity.User;
import com.nowcoder.community.service.UserService;
import com.nowcoder.community.util.CookieUtil;
import com.nowcoder.community.util.HostHolder;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;
import org.springframework.security.core.Authentication;
import org.springframework.security.core.context.SecurityContextHolder;
import org.springframework.security.core.context.SecurityContextImpl;
import org.springframework.stereotype.Component;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.net.Authenticator;
import java.util.Date;

@Component
public class LoginTicketInterceptor implements HandlerInterceptor {

    @Autowired
    private UserService userService;

    @Autowired
    private HostHolder hostHolder;

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        // 从Cookie中获取凭证
        String ticket = CookieUtil.getValue(request, "ticket");
        if (ticket != null) {
            // 查询凭证
            LoginTicket loginTicket = userService.findLoginTicket(ticket);
            // 检查凭证是否有效
            if (loginTicket != null && loginTicket.getStatus() == 0 && loginTicket.getExpired().after(new Date())) {
                // 根据凭证查用户
                User user = userService.findUserById(loginTicket.getUserId());
                // 在本次请求中持有用户，多线程，利用ThreadLocal
                hostHolder.setUser(user);
            }
        }
        return true;
    }
	// 在模板引擎调用之前把这个User存放到modelAndView里面
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        User user = hostHolder.getUser();
        if (user != null && modelAndView != null) {
            modelAndView.addObject("loginUser", user);
        }
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        hostHolder.clear();
        SecurityContextHolder.clearContext();
    }
}
```

写好拦截器就要注入到 WebMvcConfig：

```java
package com.nowcoder.community.config;

import com.nowcoder.community.controller.interceptor.AlphaInterceptor;
import com.nowcoder.community.controller.interceptor.LoginRequiredInterceptor;
import com.nowcoder.community.controller.interceptor.LoginTicketInterceptor;
import com.nowcoder.community.controller.interceptor.MessageInterceptor;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class WebMvcConfig implements WebMvcConfigurer {
    @Autowired
    private AlphaInterceptor alphaInterceptor;

    @Autowired
    private LoginTicketInterceptor loginTicketInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(alphaInterceptor)
                .excludePathPatterns("/**/*.css", "/**/*.js", "/**/*.png", "/**/*.jpg", "/**/*.jpeg")
                .addPathPatterns("/register", "/login");

        registry.addInterceptor(loginTicketInterceptor)
                .excludePathPatterns("/**/*.css", "/**/*.js", "/**/*.png", "/**/*.jpg", "/**/*.jpeg");
}
```

然后就是处理页面了。

