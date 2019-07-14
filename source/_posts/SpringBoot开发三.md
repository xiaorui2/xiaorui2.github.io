---
title: SpringBoot开发三
date: 2019-07-14 09:52:57
tags: SpringBoot
categories: SpringBoot项目
---

# 需求介绍-SpringMVC

理解`HTTP`；

服务层的三层架构：表现层，业务层，数据层，浏览器访问服务器先访问表现层，期待表现层返回一些数据，表现层呢就访问业务层处理业务，而业务层在处理业务的时候会调用数据层请求数据和处理数据

![](1.png)

`SpringMVC`是一种设计模式，也是分为三层。

![](2.png)

使用的核心组件是：`DispatcherServlet`

![](3.png)

`ViewresResolver`：视图解析视图层

`HandleMapping`：处理映射的一个组件，我们敲一个路径与Controller相匹配。

更细节的可以看：

![](4.png)

然后码一个实现的`MVC`的代码有助理解一下

# 代码

首先要理解怎么获取请求对象和响应对象以及如何确定发送方法：

第一个返回请求对象的结果

![](5.png)

使用`Post`的方法的时候，需要`html`提交表单，那么对应的网页的数据的名字要和`Controller`的对应方法获取参数名字要相同

`student.html`：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>增加学生</title>
</head>
<body>

<form method="post" action="/community/student">
    <p>
        姓名： <input type="text" name="name">
    </p>
    <p>
        年龄： <input type="text" name="age">
    </p>
    <p>
        <input type="submit" name="保存">
    </p>
</form>

</body>
</html>
```

如果你要返回一个动态的`html`的话就需要把数据传到模板文件上，

```java
<!DOCTYPE html>
<html lang="en" xmlns:th="https://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Teacher</title>
</head>
<body>
// 这里面取到的数据就是在Controller里面的方法传到这里才能正确的取到
<p th:text="${name}"></p>
<p th:text="${age}"></p>

</body>
</html>
```

那么对应的方法我们就写到`Controller`文件里面，具体的实现的代码的方法就如下

`AlphaController`：

```java
package com.nowcoder.community.controller;

import com.nowcoder.community.service.AlphaService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;
import java.util.*;

@Controller
public class AlphaController {

    @Autowired
    private AlphaService alphaService;

    @RequestMapping("/hello")
    @ResponseBody
    public String sayHello() {
        return "Hello Spring Boot!";
    }

    @RequestMapping("/data")
    @ResponseBody
    public String getData() {
        return alphaService.find();
    }

    @RequestMapping("/http")
    public void http(HttpServletRequest request, HttpServletResponse response) {
        //获取请求
        System.out.println(request.getMethod());
        System.out.println(request.getServletPath());
        //消息头
        Enumeration<String> enumeration = request.getHeaderNames();
        while (enumeration.hasMoreElements()) {
            String name = enumeration.nextElement();
            String value = request.getHeader(name);
            System.out.println(name+": "+value);
        }
        System.out.println(request.getParameter("code"));

        //给浏览器返回响应数据
        response.setContentType("text/html;charset=utf-8");
        try {
            PrintWriter writer = response.getWriter();
            writer.write("<h1>牛客网</h1>");
        }
        catch (IOException e) {
            e.printStackTrace();
        }
        System.out.println();
    }

    //Get请求
    // /student?current=1&limit=2
    // 看参数可以理解怎么把url后面带的参数给获取到，以及做更详细的说明
    @RequestMapping(path = "/students", method = RequestMethod.GET)
    @ResponseBody
    public String getStudents(@RequestParam(name = "current", required = false, defaultValue = "1") int current,
                             @RequestParam(name = "limit", required = false, defaultValue = "10") int limit) {
        System.out.println(current);
        System.out.println(limit);
        return "some student";
    }

    // /student/123
    @RequestMapping(path = "/student/{id}", method = RequestMethod.GET)
    @ResponseBody
    public String getStudent(@PathVariable("id") int id) {
        System.out.println(id);
        return "a student";
    }

    //Post请求
    @RequestMapping(path = "/student", method = RequestMethod.POST)
    @ResponseBody
    public String saveStudent(String name, int age) {
        System.out.println(name);
        System.out.println(age);
        return "success";
    }

    //响应Html数据
    @RequestMapping(path = "/teacher", method = RequestMethod.GET)
    public ModelAndView getTeacher() {
        ModelAndView modelAndView = new ModelAndView();
        // 你要把返回的数据传到ModelAndView上才能被模板正确的获取到
        modelAndView.addObject("name","张三");
        modelAndView.addObject("age",30);
        modelAndView.setViewName("/demo/view");
        return modelAndView;
    }
	// 这个方法返回html的话，最后就需要return路径
    @RequestMapping(path = "/school", method = RequestMethod.GET)
    public String getSchool(Model model) {
        model.addAttribute("name","背景大学");
        model.addAttribute("age",80);
        return "/demo/view";
    }

    //响应Json数据（异步请求）
    // Java对象 -> Json对象 -> Js对象
    @RequestMapping(path = "/emp", method = RequestMethod.GET)
    @ResponseBody // 加上返回一个Body否则就返回了一个html
    public Map<String, Object> getEmp() {
        Map<String, Object> map = new HashMap<>();
        map.put("name","张三");
        map.put("sal",8000);
        map.put("age",30);
        return map;
    }
}
```

