---
title: SpringBoot开发十二-账号设置
date: 2019-08-18 15:40:27
tags: SpringBoot
categories: SpringBoot项目
---

# 需求介绍—账号设置

账号设置里面的上传头像（文件）

首先请求必须是一个 POST 请求，其次表单的属性 enctype = "multipart/form-data"

然后就是利用 MultipartFile 处理上传文件。

然后就是访问账号设置页面，上传头像，获取头像。

# 代码实现

我们的头像上传之后是存放到我们的服务器硬盘之上，所以我们需要在 application.properties配置一下我们的资源上传之后是存放到了哪里

```
# community
community.path.domain=http://localhost:8080
community.path.upload=e:/upload
```

我们上传完文件最终是需要更新用户的 HeaderUrl，所以 Service 就需要提供一个方法改变这个 URL，然后上传文件的事情我们就在 Controller 里面解决掉，业务层只解决更新路径的这个业务就可以了。

那么在 UserService 里面追加一个方法更新用户的 URL

```java
public int updateHeader(int userId, String headerUrl) {
    return userMapper.updateHeader(userId, headerUrl);
}
```

首先新建一个 UserController 实现对于用户的一些请求

```java
package com.nowcoder.community.controller;

import com.nowcoder.community.annotation.LoginRequired;
import com.nowcoder.community.entity.User;
import com.nowcoder.community.service.FollowService;
import com.nowcoder.community.service.LikeService;
import com.nowcoder.community.service.UserService;
import com.nowcoder.community.util.CommunityConstant;
import com.nowcoder.community.util.CommunityUtil;
import com.nowcoder.community.util.HostHolder;
import org.apache.commons.lang3.StringUtils;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.multipart.MultipartFile;

import javax.jws.WebParam;
import javax.servlet.http.HttpServletResponse;
import java.io.File;
import java.io.FileInputStream;
import java.io.IOException;
import java.io.OutputStream;

@Controller
@RequestMapping("/user")
public class UserController implements CommunityConstant {
    private static final Logger logger = LoggerFactory.getLogger(UserController.class);
	// 上传路径要注入进来
    @Value("${community.path.upload}")
    private String uploadPath;
	// 域名也需要用到
    @Value("${community.path.domain}")
    private String domain;
	// 需要用到项目的访问路径
    @Value("${server.servlet.context-path}")
    private String contextPath;

    @Autowired
    private UserService userService;

    @Autowired
    private HostHolder hostHolder;

    @LoginRequired
    @RequestMapping(path = "/setting", method = RequestMethod.GET)
    public String getSettingPage() {
        return "/site/setting";
    }

    @LoginRequired
    @RequestMapping(path = "/upload", method = RequestMethod.POST)
    public String uploadHeader(MultipartFile headerImage, Model model) {
        if (headerImage == null) {
            model.addAttribute("error", "你还没有上传图片");
            return "/site/setting";
        }
		// 上传这个文件，要更换一下文件名不能用上传的文件名
        String fileName = headerImage.getOriginalFilename();// 得到原始的文件名
        String suffix = fileName.substring(fileName.lastIndexOf('.'));
        if (StringUtils.isBlank(suffix)) {
            model.addAttribute("error", "文件的格式不正确");
            return "/site/setting";
        }
        // 生成随机的文件名
        fileName = CommunityUtil.generateUUID() + suffix;
        // 确定文件存放的位置
        File dest = new File(uploadPath + "/" +fileName);
        try {
            headerImage.transferTo(dest);
        } catch (IOException e) {
            logger.error("上传文件失败：" + e.getMessage());
            throw new RuntimeException("上传文件失败，服务器发生异常" + e);
        }
        // 更新当前用户头像的路径（web路径）
        // http://localhost:8080/community/user/header/xxx.png
        User user = hostHolder.getUser();
        String headerUrl = domain + contextPath + "/user/header/" + fileName;
        userService.updateHeader(user.getId(), headerUrl);
        return "redirect:/index";
    }

    @RequestMapping(path = "/header/{fileName}", method = RequestMethod.GET)
    public void getHeader(@PathVariable("fileName") String fileName, HttpServletResponse httpServletResponse) {
        // 服务器存放路径
        fileName = uploadPath + "/" + fileName;
        // 文件的后缀
        String suffix = fileName.substring(fileName.lastIndexOf('.'));
        // 响应图片
        httpServletResponse.setContentType("image/" + suffix);
       	// 因为图片是二进制，所以要先获得字节流
        try (
                OutputStream os = httpServletResponse.getOutputStream();
                FileInputStream fis = new FileInputStream(fileName);
        ) {
            byte[] buffer = new byte[1024];
            int b = 0;
            while ((b = fis.read(buffer)) != -1) {
                os.write(buffer, 0, b);
            }
        } catch (IOException e) {
            logger.error("读取头像失败：" + e.getMessage());
        }
    }
}
```

最后就是处理页面的逻辑了。