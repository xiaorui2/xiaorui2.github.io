---
title: SpringBoot开发五-社区首页开发
date: 2019-08-11 10:59:20
tags: SpringBoot
categories: SpringBoot项目
---

# 需求介绍—社区首页

根据之前的学习，我们一般都是先按照`DAO->Service->Controller`这个顺序去开发

分布实现：

开发社区首页，显示前十个帖子。

开发分页组件，分页显示所有的帖子

# 代码实现

首先我们要知道贴子我们是放在`discuss_post`这个表里面，所以我们的操作都是根据这个表来操作。

那第一步来写一下`DiscussPost`实体类对应这个表里面的字段。

```java
package com.nowcoder.community.entity;

import java.util.Date;
public class DiscussPost {
    private int id;

    private int userId;

	private String title;
    
    private String content;

    private int type;

    private int status;

    private Date createTime;

    private int commentCount;

    private double score;


    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public int getUserId() {
        return userId;
    }

    public void setUserId(int userId) {
        this.userId = userId;
    }

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public String getContent() {
        return content;
    }

    public void setContent(String content) {
        this.content = content;
    }

    public int getType() {
        return type;
    }

    public void setType(int type) {
        this.type = type;
    }

    public int getStatus() {
        return status;
    }

    public void setStatus(int status) {
        this.status = status;
    }

    public Date getCreateTime() {
        return createTime;
    }

    public void setCreateTime(Date createTime) {
        this.createTime = createTime;
    }

    public int getCommentCount() {
        return commentCount;
    }

    public void setCommentCount(int commentCount) {
        this.commentCount = commentCount;
    }

    public double getScore() {
        return score;
    }

    public void setScore(double score) {
        this.score = score;
    }

    @Override
    public String toString() {
        return "DiscussPost{" +
                "id=" + id +
                ", userId=" + userId +
                ", title='" + title + '\'' +
                ", content='" + content + '\'' +
                ", type=" + type +
                ", status=" + status +
                ", createTime=" + createTime +
                ", commentCount=" + commentCount +
                ", score=" + score +
                '}';
    }
}

```

那么我们对应去开发`DiscussPostMapper`，完成对应对于这个数据库操作的函数声明。

```java
package com.nowcoder.community.dao;

import com.nowcoder.community.entity.DiscussPost;
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Param;

import java.util.List;

@Mapper
public interface DiscussPostMapper {

    /* 查到的事多个帖子，所以是集合，这个userId是为了开发我的主页的时候才需要，但是首页的时候就不传，
    得到的是0，那么对应的就是一个动态的sql*/
    List<DiscussPost> selectDiscussPosts(int userId, int offset, int limit);

    // @Param 用来为参数取别名
    //如果需要动态的拼一个条件，在<if>里面使用，并且这个方法有且只有一个参数，这个参数前面必须取别名
    // 这个userId和上面是一样的功能
    int selectDiscussPostRows(@Param("userId") int userId);

    int insertDiscussPost(DiscussPost discussPost);
}

```

那么函数声明了，就要去`discusspost-mapper.xml`文件里面写实现的了

```java
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.nowcoder.community.dao.DiscussPostMapper">

    <sql id="selectFields">
        id, user_id, title, content, type, status, create_time, comment_count, score
    </sql>

    <sql id="insertFields">
        user_id, title, content, type, status, create_time, comment_count, score
    </sql>
    
	// 如果返回的类型不是Java自带的类型的话，就需要加resultType，表示返回类型
    <select id="selectDiscussPosts" resultType="DiscussPost">
        select <include refid="selectFields"></include>
        from discuss_post
        where status != 2
        <if test="userId!=0">
            and user_id = #{userId}
        </if>
        order by type desc, create_time desc
        limit #{offset}, #{limit}
    </select>
    
    <select id="selectDiscussPostRows" resultType="int">
        select count(id)
        from discuss_post
        where status != 2
        <if test="userId!=0">
            and user_id = #{userId}
        </if>
    </select>
</mapper>
```

至此数据访问层就开发完了，那么要继续开发业务组件`DiscussPostService`。

```java
package com.nowcoder.community.service;

import com.nowcoder.community.dao.DiscussPostMapper;
import com.nowcoder.community.entity.DiscussPost;
import com.nowcoder.community.util.SensitiveFilter;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.web.util.HtmlUtils;

import java.util.List;

@Service
public class DiscussPostService {

    @Autowired
    private DiscussPostMapper discussPostMapper;
	// 查询帖子
    public List<DiscussPost> findDiscussPosts(int userId, int offset, int limit) {
        return discussPostMapper.selectDiscussPosts(userId, offset, limit);
    }
	// 查询行数的方法
    public int findDiscussPostRows(int userId) {
        return discussPostMapper.selectDiscussPostRows(userId);
    }
}
```

但是现在有个问题就是在平时的时候我们在页面上显示的肯定都是显示`username`而不是`userId`所以我们需要写个方法把两者关联起来，因为这个只涉及到`User`那么我们可以对写`UserService`对其进行操作

```java
package com.nowcoder.community.service;

import com.nowcoder.community.dao.LoginTicketMapper;
import com.nowcoder.community.dao.UserMapper;
import com.nowcoder.community.entity.User;

import java.util.Date;
import java.util.HashMap;
import java.util.Map;
import java.util.Random;
import java.util.concurrent.TimeUnit;

@Service
public class UserService {
    @Autowired
    private UserMapper userMapper;

    public User findUserById(int id) {
       return userMapper.selectById(id);
}
```

那么至此业务层的逻辑我们就写完了，现在就开始写表现层`DiscussController`：

我们先查前10条的帖子

```java
package com.nowcoder.community.controller;

import com.nowcoder.community.entity.DiscussPost;
import com.nowcoder.community.entity.Page;
import com.nowcoder.community.entity.User;
import com.nowcoder.community.service.DiscussPostService;
import com.nowcoder.community.service.LikeService;
import com.nowcoder.community.service.UserService;
import com.nowcoder.community.util.CommunityConstant;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

// 初始化界面以及分页功能点实现
@Controller
public class HomeController implements CommunityConstant {

    @Autowired
    private DiscussPostService discussPostService;

    @Autowired
    private UserService userService;

    @RequestMapping(path = "/index", method = RequestMethod.GET)
    public String getIndexPage(Model model) {
        // 方法调用之前，SpringMVC会自动实例化Model和Page，并将Page注入到Model中
        // 所以，在thymeleaf中可以直接访问Page对象中的数据。
        List<DiscussPost> list = discussPostService.findDiscussPosts(0,0,10);
        // 把上面这个集合遍历一遍，但是它们的数据是不完整的，比方说我们查到的只是id不是userId
        List<Map<String, Object>> discussPosts = new ArrayList<>();
        if (list != null) {
            for(DiscussPost post : list) {
                Map<String, Object> map = new HashMap<>();
                map.put("post", post);
                User user = userService.findUserById(post.getUserId());
                map.put("user", user);

                discussPosts.add(map);
            }
        }
        // 把值传到这个模板里面
        model.addAttribute("discussPosts", discussPosts);
        return "/index";
    }
}
```

然后就是处理对应的页面，我这里就不写了，基本的功能点在这了，运行之后可以查到表里面的前十条数据如下图所示：

![](1.png)

现在就继续完善分页的功能，那么现在客户端要给服务端除了之前传递的数据还要传递现在是第几页这些其他的信息，服务端也需要根据传来的信息确定这个页码是否超过了能有的最大的页码（可以根据数据库中帖子的数量很分页每页显示多少条来计算得到），因为这个数据传递的很频繁所以我们单独写个组件来实现。

那么我们先实现`Page`来确定分页的一些条件

```java
package com.nowcoder.community.entity;

/**
 * 封装分页相关的信息
 */
public class Page {

    // 页面传给服务端的数据
    // 当前页码
    private int current = 1;

    // 显示上限
    private int limit = 10;

    // 服务端查到的
    // 数据总数（用于计算总页数）
    private int rows;

    // 查询路径(用于复用分页链接）
    private String path;

    public int getCurrent() {
        return current;
    }

    public void setCurrent(int current) {
        if (current >= 1) {
            this.current = current;
        }
    }

    public int getLimit() {
        return limit;
    }

    public void setLimit(int limit) {
        if (limit >= 1 && limit <= 100) {
            this.limit = limit;
        }
    }

    public int getRows() {
        return rows;
    }

    public void setRows(int rows) {
        if (rows >= 0) {
            this.rows = rows;
        }
    }

    public String getPath() {
        return path;
    }

    public void setPath(String path) {
        this.path = path;
    }

    /**
     * 获取当前页的起始行
     * @return
     */
    public int getOffset() {
        // current * limit - limit
        return (current - 1) * limit;
    }

    /**
     * 获取总页数
     * @return
     */
    public int getTotal() {
        // rows / limit
        if (rows % limit == 0) {
            return rows / limit;
        }
        else {
            return rows / limit + 1;
        }
    }

    /**
     * 获取起始页码
     */
    public int getFrom() {
        int from = current - 2;
        return from < 1 ? 1 : from;
    }

    /**
     * 获取结束页码
     * @return
     */
    public int getTo() {
        int to = current + 2;
        return to > getTotal() ? getTotal() : to;
    }
}

```

那么要改造`HomeController`来实现分页：

```java
package com.nowcoder.community.controller;

import com.nowcoder.community.entity.DiscussPost;
import com.nowcoder.community.entity.Page;
import com.nowcoder.community.entity.User;
import com.nowcoder.community.service.DiscussPostService;
import com.nowcoder.community.service.LikeService;
import com.nowcoder.community.service.UserService;
import com.nowcoder.community.util.CommunityConstant;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

// 初始化界面以及分页功能点实现
@Controller
public class HomeController implements CommunityConstant {

    @Autowired
    private DiscussPostService discussPostService;

    @Autowired
    private UserService userService;

    @RequestMapping(path = "/index", method = RequestMethod.GET)
    public String getIndexPage(Model model, Page page) {
        // 方法调用之前，SpringMVC会自动实例化Model和Page，并将Page注入到Model中
        // 所以，在thymeleaf中可以直接访问Page对象中的数据。
        page.setRows(discussPostService.findDiscussPostRows(0));
        page.setPath("/index");
        List<DiscussPost> list = 		discussPostService.findDiscussPosts(0,page.getOffset(),page.getLimit());
        // 把上面这个集合遍历一遍，但是它们的数据是不完整的，比方说我们查到的只是id不是userId
        List<Map<String, Object>> discussPosts = new ArrayList<>();
        if (list != null) {
            for(DiscussPost post : list) {
                Map<String, Object> map = new HashMap<>();
                map.put("post", post);
                User user = userService.findUserById(post.getUserId());
                map.put("user", user);

                discussPosts.add(map);
            }
        }
        // 把值传到这个模板里面
        model.addAttribute("discussPosts", discussPosts);
        return "/index";
    }
}
```

那么还要在页面上动态的配置好对应的链接

```html
<nav class="mt-5" th:if="${page.rows>0}" th:fragment="pagination">
					<ul class="pagination justify-content-center">
						<li class="page-item">
							<a class="page-link" th:href="@{${page.path}(current=1)}">首页</a>
						</li>
						<li th:class="|page-item ${page.current==1?'disabled':''}|">
							<a class="page-link" th:href="@{${page.path}(current=${page.current-1})}">上一页</a></li>
						<li th:class="|page-item ${i==page.current?'active':''}|" th:each="i:${#numbers.sequence(page.from,page.to)}">
							<a class="page-link" href="#" th:text="${i}">1</a>
						</li>
						<li th:class="|page-item ${page.current==page.total?'disabled':''}|">
							<a class="page-link" th:href="@{${page.path}(current=${page.current+1})}">下一页</a>
						</li>
						<li class="page-item">
							<a class="page-link" th:href="@{${page.path}(current=${page.total})}">末页</a>
						</li>
					</ul>
				</nav>
```

那么至此分页的功能点就写完了，经测试功能正常，首页能正常测试