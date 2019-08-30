---
title: SpringBoot开发十七-显示评论
date: 2019-08-30 11:27:30
tags: SpringBoot
categories: SpringBoot项目
---

# 需求介绍

显示评论，还是我们之前做的流程。

数据层：根据实体查询一页的评论数据，以及根据实体查询评论的数量

业务层：处理查询评论的业务，处理查询评论数量的业务

表现层：同时显示帖子详情数据时显示该帖子的所有的评论的数量和数据

# 代码介绍

首先新增一个实体类 Comment 

```java
package com.nowcoder.community.entity;

import java.util.Date;

public class Comment {
    private int id;
    private int userId;
    private int entityType;
    private int entityId;
    private int targetId;
    private String content;
    private int status;
    private Date createTime;

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

    public int getEntityType() {
        return entityType;
    }

    public void setEntityType(int entityType) {
        this.entityType = entityType;
    }

    public int getEntityId() {
        return entityId;
    }

    public void setEntityId(int entityId) {
        this.entityId = entityId;
    }

    public int getTargetId() {
        return targetId;
    }

    public void setTargetId(int targetId) {
        this.targetId = targetId;
    }

    public String getContent() {
        return content;
    }

    public void setContent(String content) {
        this.content = content;
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

    @Override
    public String toString() {
        return "Comment{" +
                "id=" + id +
                ", userId=" + userId +
                ", entityType=" + entityType +
                ", entityId=" + entityId +
                ", targetId=" + targetId +
                ", content='" + content + '\'' +
                ", status=" + status +
                ", createTime=" + createTime +
                '}';
    }
}

```

同理新建数据层 CommentMapper 

```java
package com.nowcoder.community.dao;

import com.nowcoder.community.entity.Comment;
import org.apache.ibatis.annotations.Mapper;

import java.util.List;

@Mapper
public interface CommentMapper {
    // 根据实体来查询到的评论，要分页
    List<Comment> selectCommentsByEntity(int entityType, int entityId, int offset, int limit);

    int selectCountByEntity(int entityType, int entityId);
}
```

然后编写对应的 comment-mapper.xml 实现对应的 sql 语句

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.nowcoder.community.dao.CommentMapper">

    <sql id="selectFields">
        id, user_id, entity_type, entity_id, target_id, content, status, create_time
    </sql>

    <select id="selectCommentsByEntity" resultType="Comment">
        select <include refid="selectFields"></include>
        from comment
        where status = 0
        and entity_type = #{entityType}
        and entity_id = #{entityId}
        order by create_time asc
        limit #{offset}, #{limit}
    </select>

    <select id="selectCountByEntity" resultType="int">
        select count(id)
        from comment
        where status = 0
        and entity_type = #{entityType}
        and entity_id = #{entityId}
    </select>
</mapper>
```

然后写业务层 CommentService 调用数据层的方法

```java
package com.nowcoder.community.service;

import com.nowcoder.community.dao.CommentMapper;
import com.nowcoder.community.entity.Comment;
import com.nowcoder.community.util.CommunityConstant;
import com.nowcoder.community.util.SensitiveFilter;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Isolation;
import org.springframework.transaction.annotation.Propagation;
import org.springframework.transaction.annotation.Transactional;
import org.springframework.web.util.HtmlUtils;

import java.util.List;

@Service
public class CommentService implements CommunityConstant{
    @Autowired
    private CommentMapper commentMapper;

    public List<Comment> findCommentsByEntity(int entityType, int entityId, int offset, int limit) {
        return commentMapper.selectCommentsByEntity(entityType, entityId, offset, limit);
    }

    public int findCommentCount(int entityType, int entityId) {
        return commentMapper.selectCountByEntity(entityType, entityId);
    }
}
```

然后在帖子详情的页面，重构一下 getDiscussPost 方法，因为之前只是显示了帖子内容的详情，现在要增加对应的评论，所以重写一下对应的方法。

```java
// 因为熟悉了 mysql 的表，我们知道我的评论是有着对应的实体类型评论，它是一个常量代表，所以要在我们确定好的常量接口里面声明这些常量，然后再去 CommentController 里面重构 getDiscussPost 方法
/**
* 实体类型：帖子
*/
int ENTITY_TYPE_POST = 1;

/**
* 实体类型：评论
*/
int ENTITY_TYPE_COMMENT = 2;

/**
* 实体类型: 用户
*/
int ENTITY_TYPE_USER = 3;


// 补充对应的评论的 commentService
@Autowired
private CommentService commentService;

@RequestMapping(path = "/detail/{discussPostId}", method = RequestMethod.GET)
    public String getDiscussPost(@PathVariable("discussPostId") int discussPostId, Model model, Page page) {
        DiscussPost discussPost = discussPostService.findDiscussPostById(discussPostId);
        model.addAttribute("post", discussPost);
        // 查帖子作者
        User user = userService.findUserById(discussPost.getUserId());
        model.addAttribute("user", user);
        // 查评论分页信息
        page.setLimit(5);
        page.setPath("/discuss/detail/" + discussPostId);
        page.setRows(discussPost.getCommentCount());

        // 评论：给帖子的评论
        // 回复：给评论的评论
        // 评论列表
        List<Comment> commentList = commentService.findCommentsByEntity(
                1, discussPost.getId(), page.getOffset(), page.getLimit());
        // 评论VO列表
        List<Map<String, Object>> commentVoList = new ArrayList<>();
        if (commentList != null) {
            for (Comment comment : commentList) {
                // 一个评论的VO
                Map<String, Object> commentVo = new HashMap<>();
                // 评论
                commentVo.put("comment", comment);
                // 作者
                commentVo.put("user", userService.findUserById(comment.getUserId()));
                // 回复列表
                List<Comment> replyList = commentService.findCommentsByEntity(
                        ENTITY_TYPE_COMMENT, comment.getId(), 0, Integer.MAX_VALUE);
                // 回复VO列表
                List<Map<String, Object>> replyVoList = new ArrayList<>();
                if (replyList != null) {
                    for (Comment reply : replyList) {
                        Map<String, Object> replyVo = new HashMap<>();
                        replyVo.put("reply", reply);
                        replyVo.put("user", userService.findUserById(reply.getUserId()));
                        // 回复的目标
                        User target = reply.getTargetId() == 0 ? null : userService.findUserById(reply.getTargetId());
                        replyVo.put("target", target);
                        replyVoList.add(replyVo);
                    }
                }

                commentVo.put("replys", replyVoList);
                // 回复数量
                int replyCount = commentService.findCommentCount(ENTITY_TYPE_COMMENT, comment.getId());
                commentVo.put("replyCount", replyCount);

                commentVoList.add(commentVo);
            }
        }
        model.addAttribute("comments", commentVoList);
        return "/site/discuss-detail";
    }
```

然后就处理页面了