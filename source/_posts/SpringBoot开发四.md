---
title: SpringBoot开发四
date: 2019-07-14 10:59:09
tags: SpringBoot
categories: SpringBoot项目
---

# 需求介绍-MyBatis入门

首先就是安装`Mysql Server` 和`Mysql Workbench`。

`SqlSessionFactory`：用于创建`SqlSession`的工厂类

`SqlSession`：`MyBatis`的核心组件用于向数据库执行`SQL`

`XML`文件：对`MyBatis`底层做一些配置。

`Mapper`接口：也就是`DAO`接口，常称为`Mapper`

`Mapper`映射器：用于编写`SQL`，并且将`SQL`和实体类映射的组件采用`XML`，注解都可以实现。

编写一些操作用户数据的代码。

# 代码

首先要引用`MyBatis`和`Mysql`的依赖，在`pom.xml`文件里。

```
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.16</version>
</dependency>
<dependency>
	<groupId>org.mybatis.spring.boot</groupId>
	<artifactId>mybatis-spring-boot-starter</artifactId>
	<version>2.0.1</version>
</dependency>
```

然后配置一下`Mysql`：

```
# DataSourceProperties
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/community?characterEncoding=utf-8&useSSL=false&serverTimezone=Hongkong
spring.datasource.username=root
spring.datasource.password=****（数据库密码）
spring.datasource.type=com.zaxxer.hikari.HikariDataSource
spring.datasource.hikari.maximum-pool-size=15
spring.datasource.hikari.minimum-idle=5
spring.datasource.hikari.idle-timeout=30000

# MybatisProperties
mybatis.mapper-locations=classpath:mapper/*.xml
mybatis.type-aliases-package=com.nowcoder.community.entity
mybatis.configuration.useGeneratedKeys=true
mybatis.configuration.mapUnderscoreToCamelCase=true
```

对`User`表进行数据处理，首先要写个实体类对应表，封装表里面的数据，方便我们去处理，写在`entity`下面：

`User`：

```java
package com.nowcoder.community.entity;

import java.util.Date;

public class User {

    private int id;
    private String username;
    private String password;
    private String salt;
    private String email;
    private int type;
    private int status;
    private String activationCode;
    private String headerUrl;
    private Date createTime;

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public String getSalt() {
        return salt;
    }

    public void setSalt(String salt) {
        this.salt = salt;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
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

    public String getActivationCode() {
        return activationCode;
    }

    public void setActivationCode(String activationCode) {
        this.activationCode = activationCode;
    }

    public String getHeaderUrl() {
        return headerUrl;
    }

    public void setHeaderUrl(String headerUrl) {
        this.headerUrl = headerUrl;
    }

    public Date getCreateTime() {
        return createTime;
    }

    public void setCreateTime(Date createTime) {
        this.createTime = createTime;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", password='" + password + '\'' +
                ", salt='" + salt + '\'' +
                ", email='" + email + '\'' +
                ", type=" + type +
                ", status=" + status +
                ", activationCode='" + activationCode + '\'' +
                ", headerUrl='" + headerUrl + '\'' +
                ", createTime=" + createTime +
                '}';
    }

}
```

然后再DAO层写一个接口（组件），写一些操作数据的方法的声明

```java
package com.nowcoder.community.dao;

import com.nowcoder.community.entity.User;
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Param;

@Mapper
public interface UserMapper {

    User selectById(int id);
    User selectByName(String username);
    User selectByEmail(String email);

    int insertUser(User user);
    int updateStatus(@Param("id") int id, @Param("status") int status);
    int updateHeader(@Param("id") int id, @Param("headerUrl") String headerUrl);
    int updatePassword(@Param("id") int id, @Param("password") String password);

}
```

然后我们就需要去写具体实现数据操作的`xml`文件了，就去`mapper`文件夹下面写，

首先你要在`<mapper> `里面写你写的这个是为哪个`Mapper`服务的，具体实现的时候就是写`sql`语句，你需要写`id`对应着你接口写的那个方法，才能够真正的去实现接口声明的方法才可以。

有个问题就是在接口声明方法的时候是有参数的嘛，你要确定这个是个什么参数，如果是`java`自带的参数类型就不用去管它，但是如果是复杂的参数比方说是个`bean`就需要声明另一个参数，具体见`insertUser`实现的那块，如果有对应到数据表的列名是数据库自己生成的话就不需要传进去需要另外取一个参数。

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.nowcoder.community.dao.UserMapper">

    <sql id="insertFields">
        username, password, salt, email, type, status, activation_code, header_url, create_time
    </sql>

    <sql id="selectFields">
        id, username, password, salt, email, type, status, activation_code, header_url, create_time
    </sql>

    <select id="selectById" resultType="User">
        select <include refid="selectFields"></include>
        from user
        where id = #{id}
    </select>

    <select id="selectByName" resultType="User">
        select <include refid="selectFields"></include>
        from user
        where username = #{username}
    </select>

    <select id="selectByEmail" resultType="User">
        select <include refid="selectFields"></include>
        from user
        where email = #{email}
    </select>

    <insert id="insertUser" parameterType="User" keyProperty="id">
        insert into user (<include refid="insertFields"></include>)
        values(#{username}, #{password}, #{salt}, #{email}, #{type}, #{status}, #{activationCode}, #{headerUrl}, #{createTime})
    </insert>

    <update id="updateStatus">
        update user set status = #{status} where id = #{id}
    </update>

    <update id="updateHeader">
        update user set header_url = #{headerUrl} where id = #{id}
    </update>

    <update id="updatePassword">
        update user set password = #{password} where id = #{id}
    </update>

</mapper>
```

然后写一个测试方法，看我们写的对不对，就去测试类里面实现：

`MapperTest`：

```java
package com.nowcoder.community;

import com.nowcoder.community.dao.DiscussPostMapper;
import com.nowcoder.community.dao.UserMapper;
import com.nowcoder.community.entity.DiscussPost;
import com.nowcoder.community.entity.User;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringRunner;

import java.util.Date;
import java.util.List;

@RunWith(SpringRunner.class)
@SpringBootTest
@ContextConfiguration(classes = CommunityApplication.class)
public class MapperTest {

    @Autowired
    private UserMapper userMapper;

    @Autowired
    private DiscussPostMapper discussPostMapper;


    @Test
    public void testSelectUser() {
        User user = userMapper.selectById(101);
        System.out.println(user);

        user = userMapper.selectByName("liubei");
        System.out.println(user);

        user = userMapper.selectByEmail("nowcoder101@sina.com");
        System.out.println(user);
    }

    @Test
    public void testInsertUser() {
        User user = new User();
        user.setUsername("test");
        user.setPassword("123456");
        user.setSalt("abc");
        user.setEmail("test@qq.com");
        user.setHeaderUrl("http://www.nowcoder.com/101.png");
        user.setCreateTime(new Date());

        int rows = userMapper.insertUser(user);
        System.out.println(rows);
        System.out.println(user.getId());
    }

    @Test
    public void updateUser() {
        int rows = userMapper.updateStatus(150, 1);
        System.out.println(rows);

        rows = userMapper.updateHeader(150, "http://www.nowcoder.com/102.png");
        System.out.println(rows);

        rows = userMapper.updatePassword(150, "hello");
        System.out.println(rows);
    }

    @Test
    public void testSelectPosts() {
        List<DiscussPost> list = discussPostMapper.selectDiscussPosts(149, 0, 10);
        for(DiscussPost post : list) {
            System.out.println(post);
        }

        System.out.println(discussPostMapper.selectDiscussPostRows(149));
    }

}
```

