---
title: SpringBoot开发十八-事务管理和添加评论
date: 2019-08-30 11:27:58
tags: SpringBoot
categories: SpringBoot项目
---

# 需求介绍

熟悉事务管理，并且应用到添加评论的功能。

数据层：增加评论数据，修改帖子的评论数量

业务层：处理添加评论的业务，先增加评论再更新帖子的评论数量（因为用到了两个DML操作所以要用到事务管理）

表现层：处理添加评论数据的请求，设置添加评论的表单

# 代码实现

首先模拟某一个业务，利用事务实现，首先我们要确定我们的业务要写在 Service 业务层，所以在 AlphaService 文件里面编写，我们先考虑一个事务，比方说我们注册一个用户增加一个 user，并且自动的用这个 user 的账号发个帖。那么这一个业务包含两个增加操作。

```java
// 因为涉及到用户和帖子把这两个 mapper 注入进来
@Autowired
private UserMapper userMapper;

@Autowired
private DiscussPostMapper discussPostMapper;

// REQUIRED: 支持当前事务(外部事务),如果不存在则创建新事务.
// REQUIRES_NEW: 创建一个新事务,并且暂停当前事务(外部事务).
// NESTED: 如果当前存在事务(外部事务),则嵌套在该事务中执行(独立的提交和回滚),否则就会REQUIRED一样.
// 注解实现事务    
@Transactional(isolation = Isolation.READ_COMMITTED, propagation = Propagation.REQUIRED)
    public Object save1() {
        // 新增用户
        User user = new User();
        user.setUsername("alpha");
        user.setSalt(CommunityUtil.generateUUID().substring(0, 5));
        user.setPassword(CommunityUtil.md5("123" + user.getSalt()));
        user.setEmail("alpha@qq.com");
        user.setHeaderUrl("http://image.nowcoder.com/head/99t.png");
        user.setCreateTime(new Date());
        userMapper.insertUser(user);

        // 新增帖子
        DiscussPost post = new DiscussPost();
        post.setUserId(user.getId());
        post.setTitle("Hello");
        post.setContent("新人报道!");
        post.setCreateTime(new Date());
        discussPostMapper.insertDiscussPost(post);
		// 人为造个错，确定出错的时候会不会回滚
        Integer.valueOf("abc");

        return "ok";
    }

	// 利用编程式事务
    public Object save2() {
        transactionTemplate.setIsolationLevel(TransactionDefinition.ISOLATION_READ_COMMITTED);
        transactionTemplate.setPropagationBehavior(TransactionDefinition.PROPAGATION_REQUIRED);

        return transactionTemplate.execute(new TransactionCallback<Object>() {
            @Override
            public Object doInTransaction(TransactionStatus status) {
                // 新增用户
                User user = new User();
                user.setUsername("beta");
                user.setSalt(CommunityUtil.generateUUID().substring(0, 5));
                user.setPassword(CommunityUtil.md5("123" + user.getSalt()));
                user.setEmail("beta@qq.com");
                user.setHeaderUrl("http://image.nowcoder.com/head/999t.png");
                user.setCreateTime(new Date());
                userMapper.insertUser(user);

                // 新增帖子
                DiscussPost post = new DiscussPost();
                post.setUserId(user.getId());
                post.setTitle("你好");
                post.setContent("我是新人!");
                post.setCreateTime(new Date());
                discussPostMapper.insertDiscussPost(post);
				// 人为造个错，确定出错的时候会不会回滚
                Integer.valueOf("abc");
                return "ok";
            }
        });
```

然后新建一个测试类，测试我们的方法。

```java
package com.nowcoder.community;

import com.nowcoder.community.service.AlphaService;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringRunner;

@RunWith(SpringRunner.class)
@SpringBootTest
@ContextConfiguration(classes = CommunityApplication.class)
public class TransactionTests {
    @Autowired
    private AlphaService alphaService;

    @Test
    public void testSave1() {
        Object object = alphaService.save1();
        System.out.println(object);
    }
    @Test
    public void testSave2() {
        Object object = alphaService.save2();
        System.out.println(object);
    }
}
```

结果会报错，查数据库我们也会看到这个数据是没有插到数据库里面。

事务管理熟悉了之后，我们开发添加评论的需求。

先在数据层 CommentMapper 写 insertComment 方法增加评论数据

```java
int insertComment(Comment comment);
```

然后去 comment-mapper.xml 写具体实现

```sql
<sql id="insertFields">
        user_id, entity_type, entity_id, target_id, content, status, create_time
</sql>

<insert id="insertComment" parameterType="Comment">
        insert into comment(<include refid="insertFields"></include>)
        values(#{userId},#{entityType},#{entityId},#{targetId},#{content},#{status},#{createTime})
</insert>
```

然后写更新帖子评论数量的 sql 语句，在 DiscussPostMapper 里面增加 updateCommentCount 方法

```
int updateCommentCount(int id, int commentCount);
```

在 discusspost-mapper.xml 里面写实现

```
<update id="updateCommentCount">
        update discuss_post set comment_count = #{commentCount} where id = #{id}
</update>
```

至此数据层就结束了，去开发业务层。

首先我们帖子的业务组件 DiscussPostService 中增加一个方法 updateCommentCount 更新帖子数量，使得在开发评论的时候可以依赖这个业务组件

```java
public int updateCommentCount(int id, int commentCount) {
        return discussPostMapper.updateCommentCount(id, commentCount);
}
```

然后在 CommentService 里面增加处理添加评论的业务方法 addComment 了

```java
// 因为要使用敏感词过滤把该工具注入进来
@Autowired
private SensitiveFilter sensitiveFilter;

@Autowired
private SensitiveFilter sensitiveFilter;

@Transactional(isolation = Isolation.READ_COMMITTED, propagation = Propagation.REQUIRED)
    public int addComment(Comment comment) {
        if (comment == null) {
            throw new IllegalArgumentException("参数不能为空");
        }
        // 添加评论
        comment.setContent(HtmlUtils.htmlEscape(comment.getContent()));
        comment.setContent(sensitiveFilter.filter(comment.getContent()));
        int rows = commentMapper.insertComment(comment);

        // 更新帖子评论数量
        if (comment.getEntityType() == ENTITY_TYPE_POST) {
            // 查到实体对应的评论数量
            int count = commentMapper.selectCountByEntity(comment.getEntityType(), comment.getEntityId());
            discussPostService.updateCommentCount(comment.getEntityId(), count);
        }

        return rows;
    }
```

业务方法解决，最后去表现层 CommentController 里写 addComment 方法：

```java
package com.nowcoder.community.controller;

import com.nowcoder.community.entity.Comment;
import com.nowcoder.community.entity.DiscussPost;
import com.nowcoder.community.entity.Event;
import com.nowcoder.community.event.EventProducer;
import com.nowcoder.community.service.CommentService;
import com.nowcoder.community.service.DiscussPostService;
import com.nowcoder.community.util.CommunityConstant;
import com.nowcoder.community.util.HostHolder;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

import java.util.Date;

@Controller
@RequestMapping("/comment")
public class CommentController implements CommunityConstant {
    @Autowired
    private CommentService commentService;

    @Autowired
    private HostHolder hostHolder;

    @Autowired
    private EventProducer eventProducer;

    @Autowired
    private DiscussPostService discussPostService;

    // 页面会提交内容和两个内容（实体类型和实体 ID）
    @RequestMapping(path = "/add/{discussPostId}", method = RequestMethod.POST)
    public String addComment(@PathVariable("discussPostId") int discussPostId, Comment comment) {
        // 得到当前用户的 ID
        comment.setUserId(hostHolder.getUser().getId());
        comment.setStatus(0);
        comment.setCreateTime(new Date());
        commentService.addComment(comment);
        return "redirect:/discuss/detail/" + discussPostId;
    }
}
```

最后就是处理页面了。