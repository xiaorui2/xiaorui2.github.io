---
title: SpringBoot开发十-开发登录，退出功能
date: 2019-08-13 21:43:37
tags: SpringBoot
categories: SpringBoot项目
---

# 需求介绍—开发登录，退出功能

访问登录页面：点击头部区域的链接打开登录页面

登录：

- 验证账号，密码，验证码
- 成功时生成登录凭证发放给客户端，失败时跳转回登录页面

退出：

- 将登录状态修改为失效的状态
- 跳转至往网站的首页

# 代码实现

现在我们暂时把登录凭证存到数据库里面有一张表`login_tickrt`，以后会存到`Redis`里面。

那么首先要把登录凭证的相关操作实现了，首先写个实体类对应`login_tickrt`表里的数据，将其封装起来

```java
package com.nowcoder.community.entity;

import java.util.Date;

public class LoginTicket {
    private int id;
    private int userId;
    private String ticket;
    private int status;
    private Date expired;

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

    public String getTicket() {
        return ticket;
    }

    public void setTicket(String ticket) {
        this.ticket = ticket;
    }

    public int getStatus() {
        return status;
    }

    public void setStatus(int status) {
        this.status = status;
    }

    public Date getExpired() {
        return expired;
    }

    public void setExpired(Date expired) {
        this.expired = expired;
    }

    @Override
    public String toString() {
        return "LoginTicket{" +
                "id=" + id +
                ", userId=" + userId +
                ", ticket='" + ticket + '\'' +
                ", status=" + status +
                ", expired=" + expired +
                '}';
    }
}
```

接下来实现数据访问的逻辑，那么新建一个接口`LoginTicketMapper`，对应的有以下几种方法：增加一个凭证，依据`ticket`来查凭证，修改凭证的状态。

这次我们通过注解的方式实现`sql`查询。

```java
package com.nowcoder.community.dao;

import com.nowcoder.community.entity.LoginTicket;
import org.apache.ibatis.annotations.*;

@Mapper
public interface LoginTicketMapper {

    @Insert({
            "insert into login_ticket(user_id,ticket,status,expired) ",
            "value(#{userId},#{ticket},#{status},#{expired})"
    })
    // 希望id自动生成，
    @Options(useGeneratedKeys = true, keyProperty = "id")
    int insertLoginTicket(LoginTicket loginTicket);

    @Select({
            "select id,user_id,ticket,status,expired ",
            "from login_ticket where ticket=#{ticket}"
    })
    LoginTicket selectByTicket(String ticket);

    @Update({
            "update login_ticket set status=#{status} where ticket=#{ticket}"
    })
    int updateStatus(String ticket, int status);
}
```

那么现在就开发业务层`UserService`：

```java
// 封装一个Map，返回多种情况的返回结果
public Map<String, Object> login(String username, String password, long expiredSeconds) {
        Map<String, Object> map = new HashMap<>();
        if(StringUtils.isBlank(username)) {
            map.put("usernameMsg", "账号不能为空");
        }
        if(StringUtils.isBlank(password)) {
            map.put("passwordMsg", "密码不能为空");
        }

        // 验证账号
        User user = userMapper.selectByName(username);
        if (user == null) {
            map.put("usernameMsg", "该账号不存在");
            return map;
        }
        // 验证状态
        if(user.getStatus() == 0) {
            map.put("usernameMsg", "该账号未激活");
            return map;
        }

        // 验证密码
        password = CommunityUtil.md5(password + user.getSalt());
        if (!user.getPassword().equals(password)) {
            map.put("passwordMsg", "密码不正确");
            return map;
        }

        // 生成登录凭证
        LoginTicket loginTicket = new LoginTicket();
        loginTicket.setUserId(user.getId());
        loginTicket.setTicket(CommunityUtil.generateUUID());
        loginTicket.setStatus(0);
        loginTicket.setExpired(new Date(System.currentTimeMillis() + expiredSeconds * 1000));
        loginTicketMapper.insertLoginTicket(loginTicket);

        map.put("ticket", loginTicket.getTicket());
        return map;
    }
```

那么现在写表现层，写`LoginController`来处理页面传来的请求，写能处理请求的方法就可以了。

对于记住我的勾选我们要有不同的保存时间，所以我们定义两个常量的时间比较好，在`CommunityConstant`里添加定义：

```
/**
* 默认状态的登录凭证的超时时间
*/
int DEFAULT_EXPIRED_SECONDS = 3600 * 12;

/**
* 记住状态的登录凭证的超时时间
*/
int REMEMBER_EXPIRED_SECONDS = 3600 * 24 *100;


```

辅助功能完成就写对应的表现层：

```java
// 我们在决定Cookie的有效路径,最好是用变量来显示比较方便
@Value("${server.servlet.context-path}")
private String contextPath;

@RequestMapping(path = "/login", method = RequestMethod.POST)
public String login(String username, String password, String code, boolean rememberme, Model model, HttpSession httpSession, HttpServletResponse httpServletResponse) {
        // 检查验证码
        String kaptcha = (String) httpSession.getAttribute("kaptcha");

        if (StringUtils.isBlank(kaptcha) || StringUtils.isBlank(code) || !kaptcha.equalsIgnoreCase(code)) {
            model.addAttribute("codeMsg","验证码不正确");
            return "/site/login";
        }

        // 检查账号，密码
        int expiredSeconds = rememberme ? REMEMBER_EXPIRED_SECONDS : DEFAULT_EXPIRED_SECONDS;
        Map<String, Object> map = userService.login(username, password, expiredSeconds);
        if(map.containsKey("ticket")) {
            // 需要给客户端发送一个Cookie
            Cookie cookie = new Cookie("ticket", map.get("ticket").toString());
            cookie.setPath(contextPath);
            cookie.setMaxAge(expiredSeconds);
            httpServletResponse.addCookie(cookie);
            return "redirect:/index";
        }
        else {
            model.addAttribute("usernameMsg", map.get("usernameMsg"));
            model.addAttribute("passwordMsg", map.get("passwordMsg"));
            return "/site/login";
        }
    }
```

最后开发退出：

首先数据层我们已经写好了，只需要写个业务层和表现层

```java
// 业务层调一下mapper的update方法就可以了
public void logout(String ticket) {
    loginTicketMapper.updateStatus(ticket, 1);
}
```

表现层要从`Cookie`中拿到`ticket`，然后调业务层。

```java
@RequestMapping(path = "/logout", method = RequestMethod.GET)
public String logout(@CookieValue("ticket") String ticket) {
    userService.logout(ticket);
    return "redirect:/login";
}
```

