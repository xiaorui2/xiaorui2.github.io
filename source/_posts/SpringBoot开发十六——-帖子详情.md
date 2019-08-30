---
title: SpringBoot开发十六-帖子详情
date: 2019-08-30 11:26:59
tags: SpringBoot
categories: SpringBoot项目
---

# 需求介绍

实现帖子详情，在帖子标题上增加访问详情页面的链接。

# 代码实现

开发流程：

- 首先在数据访问层新增一个方法 实现查看帖子的方法
- 业务层同理增加查询方法
- 最后在表现层处理查询请求

数据访问层增加根据帖子 id 查询出一个帖子的详细信息

```java
DiscussPost selectDiscussPostById(int id);
```

然后去 discusspost-mapper.xml 文件里写具体的实现

```sql
<select id="selectDiscussPostById" resultType="DiscussPost">
        select <include refid="selectFields"></include>
        from discuss_post
        where id = #{id}
</select>
```

数据层处理完，就去业务层增加一个方法 findDiscussPostById 根据 ID 查帖子

```java
public DiscussPost findDiscussPostById(int id) {
        return discussPostMapper.selectDiscussPostById(id);
}
```

业务层结束，去表现层 DiscussPostController 中写 getDiscussPost 方法得到帖子的详情

```java
@Autowired
private UserService userService;

@RequestMapping(path = "/detail/{discussPostId}", method = RequestMethod.GET)
    public String getDiscussPost(@PathVariable("discussPostId") int discussPostId, Model model) {
        DiscussPost discussPost = discussPostService.findDiscussPostById(discussPostId);
        model.addAttribute("post", discussPost);
        // 但是有个问题，我们这个表里得到的是 ID 但是我们在页面上得到的肯定是想得到用户的头像啊或者名字，我们可以通过得到的 ID 查帖子作者
        User user = userService.findUserById(discussPost.getUserId());
        model.addAttribute("user", user);
        return "/site/discuss-detail";
    }
```

最后就是处理对应的页面了。