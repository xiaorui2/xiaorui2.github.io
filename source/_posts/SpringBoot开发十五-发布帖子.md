---
title: SpringBoot开发十五-发布帖子
date: 2019-08-18 16:29:11
tags: SpringBoot
categories: SpringBoot项目
---

# 需求介绍

使用 AJAX 异步通信实现网页能够增量的更新呈现到页面上而不需要刷新整个页面。

现在基本上都是服务器返回 JSON 字符串来解析

# 代码实现

使用 JQuery 发送 AJAX 请求。

首先我们要有几个处理 JSON 字符串的方法，因为服务器要给浏览器返回 JSON 字符串，我们引入一个包下的 API 来处理。

```
<dependency>
	<groupId>com.alibaba</groupId>
	<artifactId>fastjson</artifactId>
	<version>1.2.58</version>
</dependency>
```

利用这个 API 开发工具方法，往往服务器向浏览器返回的JSON数据，这个JSON包含几个部分：编码，即时信息（成功还是失败啊这些），以及业务数据。

```java
public static String getJSONString(int code, String msg, Map<String, Object> map) {
		// 先定义一个JSON对象
        JSONObject json = new JSONObject();
        json.put("code", code);
        json.put("msg", msg);
    	// 把Hashmap打散放到json里面
        if (map != null) {
            for (String  key : map.keySet()) {
                json.put(key, map.get(key));
            }
        }
        return json.toJSONString();
    }

    public static String getJSONString(int code, String msg) {
        return getJSONString(code, msg, null);
    }

    public static String getJSONString(int code) {
        return getJSONString(code, null, null);
    }
```

然后写一个实例模拟 AJAX 的异步请求，那么需要在 AlphaController 里面声明一个方法处理这样的请求，

```java
// Ajax实例
    @RequestMapping(path = "/ajax", method = RequestMethod.POST)
	// 异步请求是不返回网页的，所以是返回body
    @ResponseBody
    public String testAjax(String name, int age) {
        System.out.println(name);
        System.out.println(age);
        return CommunityUtil.getJSONString(0, "ok");
    }
```

然后写页面处理，就不细致的说了。

现在就写发布帖子的功能。

那么就从数据访问层写起，那么要增加一个方法 insertDiscussPost 能增加帖子的数据

```
int insertDiscussPost(DiscussPost discussPost);
```

然后去 discusspost-mapper.xml 文件里面写方法的实现：

```sql
<sql id="insertFields">
        user_id, title, content, type, status, create_time, comment_count, score
</sql>

<insert id="insertDiscussPost" parameterType="DiscussPost" keyProperty="id">
        insert into discuss_post(<include refid="insertFields"></include>)
        values(#{userId},#{title},#{content},#{type},#{status},#{createTime},#{commentCount},#{score})
    </insert>
```

数据访问层写完，写业务层，业务层需要提供一个能够保存帖子的业务方法 addDiscussPost，添加的帖子也要利用到之前写过的过滤敏感词的方法

```java
// 先注入过滤敏感词的工具
@Autowired
private SensitiveFilter sensitiveFilter;

public int addDiscussPost(DiscussPost discussPost) {
        if (discussPost == null) {
            throw new IllegalArgumentException("参数不能为空！");
        }
        // 转义Html标签
        discussPost.setTitle(HtmlUtils.htmlEscape(discussPost.getTitle()));
        discussPost.setContent(HtmlUtils.htmlEscape(discussPost.getContent()));
        // 过滤敏感词
        discussPost.setTitle(sensitiveFilter.filter(discussPost.getTitle()));
        discussPost.setContent(sensitiveFilter.filter(discussPost.getContent()));

        return discussPostMapper.insertDiscussPost(discussPost);
    }
```

然后新建 DiscussPostController，控制视图层，那么以后涉及到帖子的都在这个 Controller 里面实现

```java
package com.nowcoder.community.controller;

import com.nowcoder.community.dao.CommentMapper;
import com.nowcoder.community.entity.*;
import com.nowcoder.community.event.EventProducer;
import com.nowcoder.community.service.CommentService;
import com.nowcoder.community.service.DiscussPostService;
import com.nowcoder.community.service.LikeService;
import com.nowcoder.community.service.UserService;
import com.nowcoder.community.util.CommunityConstant;
import com.nowcoder.community.util.CommunityUtil;
import com.nowcoder.community.util.HostHolder;
import com.sun.org.apache.xpath.internal.operations.Mod;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.ResponseBody;

import java.util.*;

@Controller
@RequestMapping("/discuss")
public class DiscussPostController implements CommunityConstant {
    @Autowired
    private DiscussPostService discussPostService;

    // 获取当前用户
    @Autowired
    private HostHolder hostHolder;

    @RequestMapping(path = "/add", method = RequestMethod.POST)
    @ResponseBody
    public String addDiscussPost(String title, String content) {
        User user = hostHolder.getUser();
        if (user == null) {
            return CommunityUtil.getJSONString(403, "您还没有登录");
        }
        DiscussPost discussPost = new DiscussPost();
        discussPost.setUserId(user.getId());
        discussPost.setTitle(title);
        discussPost.setContent(content);
        discussPost.setCreateTime(new Date());
        discussPostService.addDiscussPost(discussPost);

        // 报错的情况以后统一处理
        return CommunityUtil.getJSONString(0, "发布成功！");
    }
}

```

然后就开始处理页面，要写异步请求，就可以了。