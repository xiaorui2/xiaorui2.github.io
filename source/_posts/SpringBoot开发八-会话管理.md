---
title: SpringBoot开发七-会话管理
date: 2019-08-13 21:25:59
tags: SpringBoot
categories: SpringBoot项目
---

# 需求介绍—会话管理

利用`Cookie`和`Seesion`使得`HTTP`变成有会话的连接，写几个实例演示一下

# 代码实现

先写个例子，表示客户端第一次访问服务器，服务器端创建一个`Cookie`发送给客户端。

不管是返回什么，都是通过做出响应，都是通过`HttpServletResponse`作响应，存到`HttpServletResponse`的头部

```java
@RequestMapping(path = "/cookie/set", method = RequestMethod.GET)
@ResponseBody
public String setCookie(HttpServletResponse httpServletResponse) {
	// 创建对象
    Cookie cookie = new Cookie("code", CommunityUtil.generateUUID());
    // 指定路径，设置cookie有效范围,只有这个范围发送的时候才会发送Cookie
    cookie.setPath("/community");
	// 设置cookie生存时间，以秒为单位
	cookie.setMaxAge(60 * 10);
	// 发送cookie
	httpServletResponse.addCookie(cookie);
	return "set cookie";
}
// 查看Cookie,通过@CookieValue注解，可以在服务端得到Cookie，通过里面的变量值来得到某一个Cookie的Value值，不然那得到是头携带的所有的Cookie
@RequestMapping(path = "/cookie/get", method = RequestMethod.GET)
@ResponseBody
public String getCookie(@CookieValue("code") String code) {
    System.out.println(code);
    return "get cookie";
}
```

结果：

![](1.png)

![](2.png)

那现在看下`Session`的创建，下发，响应。

`Session`可以存任意的数据，而`Cookie`一般只能存`String`

```java
// Session示例
@RequestMapping(path = "/sesssion/set", method = RequestMethod.GET)
@ResponseBody
public String setSession(HttpSession httpSession) {
    httpSession.setAttribute("id",1);
    httpSession.setAttribute("name","Test");
    return "set Session";
}
// 从session中取值
@RequestMapping(path = "/session/get", method = RequestMethod.GET)
@ResponseBody
public String getSession(HttpSession httpSession) {
    System.out.println(httpSession.getAttribute("id"));
    System.out.println(httpSession.getAttribute("name"));
    return "get Session";
}
```





