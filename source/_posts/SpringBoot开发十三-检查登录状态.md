---
title: SpringBoot开发十三-检查登录状态
date: 2019-08-18 15:40:54
tags: SpringBoot
categories: SpringBoot项目
---

# 需求介绍—检查登录状态

防止用户知道我们的一些功能的链接，直接就进到了该页面，就像有些功能是管理员访问才能进的，就需要进行登录状态的判断。

我们知道这个功能点很多其他的功能点都需要使用，所以我们需要使用拦截器。

但是这次在方法前标示自定义注解，拦截所有的请求只处理带该注解的方法

# 代码实现

先自定义注解 LoginRequired：内容其实啥都不用写，只起到一个标示的作用，我打上这个标记就必须登录才能访问

```java
package com.nowcoder.community.annotation;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface LoginRequired {

}
```

那么我们需要在 user/setting 的两个方法上加上这个注解：

```java
@LoginRequired
@RequestMapping(path = "/setting", method = RequestMethod.GET)

@LoginRequired
@RequestMapping(path = "/upload", method = RequestMethod.POST)
```

加上后就要利用拦截器拦截带有这个注解的方法，拦截到该方法之后就判断你有没有登录，登录了可以，没登录拒绝，新建 LoginRequiredInterceptor：

```java
package com.nowcoder.community.controller.interceptor;

import com.nowcoder.community.annotation.LoginRequired;
import com.nowcoder.community.util.HostHolder;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
import org.springframework.web.method.HandlerMethod;
import org.springframework.web.servlet.HandlerInterceptor;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.lang.reflect.Method;

@Component
public class LoginRequiredInterceptor implements HandlerInterceptor {
    @Autowired
    HostHolder hostHolder;
	// 在请求最初判断是否是登录状态
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        // 首先我们只拦截方法，其他的我们不管
        if (handler instanceof HandlerMethod) {
            // 进行类型转换
            HandlerMethod handlerMethod = (HandlerMethod) handler;
            // 拦截到的Method的对象
            Method method = handlerMethod.getMethod();
            // 取到对象的注解
            LoginRequired loginRequired = method.getAnnotation(LoginRequired.class);
            if (loginRequired != null && hostHolder.getUser() == null) {
                response.sendRedirect(request.getContextPath() + "/login");
                return false;
            }
        }
        return true;
    }
}
```

最后在 WebMvcConfig 里配置一下拦截器的拦截范围：

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
    private LoginRequiredInterceptor loginRequiredInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {

        registry.addInterceptor(loginRequiredInterceptor)
                .excludePathPatterns("/**/*.css", "/**/*.js", "/**/*.png", "/**/*.jpg", "/**/*.jpeg");
}
```

