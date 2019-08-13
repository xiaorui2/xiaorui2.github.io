---
title: SpringBoot开发九-生成验证码
date: 2019-08-13 21:43:12
tags: SpringBoot
categories: SpringBoot项目
---

# 需求介绍—生成验证码

先生成随机字符串然后利用`Kaptcha API`生成验证图片

# 代码实现

先在`pom.xml`引入

```
<dependency>
	<groupId>com.github.penggle</groupId>
	<artifactId>kaptcha</artifactId>
	<version>2.3.2</version>
</dependency>
```

因为`SpringBoot`中没有整合`kaptcha`，所以我们需要自己对这个做一个配置，所以要写一个配置类`KaptchaConfig`加载到`Spring`容器里面对其做一个初始化。

```java
package com.nowcoder.community.config;

import com.google.code.kaptcha.Producer;
import com.google.code.kaptcha.impl.DefaultKaptcha;
import com.google.code.kaptcha.util.Config;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import java.util.Properties;

// 该注解表示这个是一个配置类
@Configuration
public class KaptchaConfig {
    // 实例化这个接口
    @Bean
    public Producer kaptchaProducer() {
        Properties properties = new Properties();
        properties.setProperty("kaptcha.image.width", "100");
        properties.setProperty("kaptcha.image.height", "40");
        properties.setProperty("kaptcha.textproducer.font.size", "32");
        properties.setProperty("kaptcha.textproducer.font.color", "0,0,0");
        // 验证码的字符在哪个范围里随机产生
        properties.setProperty("kaptcha.textproducer.char.string", "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ");
        // 验证码的长度
        properties.setProperty("kaptcha.textproducer.char.length", "4");
        properties.setProperty("kaptcha.noise.impl", "com.google.code.kaptcha.impl.NoNoise");
        DefaultKaptcha kaptcha = new DefaultKaptcha();
        Config config = new Config(properties);
        kaptcha.setConfig(config);
        return  kaptcha;
    }
}
```

然后再登录页面LoginController里面使用，思想是在一个方法是给浏览器返回一个html，这个html里面会包含一个验证码的路径，浏览器会再访问服务器得到这个验证码图片，所以要写一个方法向浏览器返回图片，但是在模板里会引入这个路径。

```java
@Autowired
private Producer kaptchaProducer;

@RequestMapping(path = "/kaptcha", method = RequestMethod.GET)
    public void getKaptcha(HttpServletResponse httpServletResponse, HttpSession httpSession) {
        // 生成验证码
        String text = kaptchaProducer.createText();
        BufferedImage image = kaptchaProducer.createImage(text);

        // 将验证码存入session
        httpSession.setAttribute("kaptcha", text);
       
        // 将图片输出给浏览器
        httpServletResponse.setContentType("image/png");
        try {
            OutputStream os = httpServletResponse.getOutputStream();
            ImageIO.write(image, "png", os);
        } catch (IOException e) {
            logger.error("响应验证码失败" + e.getMessage());
        }
}
```

当然你还需要在login.html文件里面改一下对应的那个验证码的路径就可以实现了。