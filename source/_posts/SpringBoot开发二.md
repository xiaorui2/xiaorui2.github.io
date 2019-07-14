---
title: SpringBoot开发二
date: 2019-07-14 09:52:38
tags: SpringBoot
categories: SpringBoot项目
---

# 需求介绍-Spring入门

主要是理解`IOC`，理解容器和`Bean`

# 代码

在`Test`里面利用`getBean`方法帮助我们看一下容器的创建：

那我首先要写一个`Bean`对象，假设是写一个访问数据库类。

`AlphaDao`（`interface`）类型：

```java
package com.nowcoder.community.dao;

public interface AlphaDao {
    String select();
}

```

然后写两个类实现这个接口体验利用容器好处：

`AlphaDaoHibernatelmpl`：

```java
package com.nowcoder.community.dao;

import org.springframework.stereotype.Repository;

@Repository("alphahibernate")//给bean重命名
public class AlphaDaoHibernatelmpl implements AlphaDao {
    @Override
    public String select() {
        return "Hefakajk";
    }
}

```

`AlphaDaoMybatisImpl`：

```java
package com.nowcoder.community.dao;

import org.springframework.context.annotation.Primary;
import org.springframework.stereotype.Repository;

@Repository
@Primary//具有更高的优先级
public class AlphaDaoMybatisImpl implements AlphaDao{
    @Override
    public String select() {
        return "fhakjhgajhga";
    }
}

```

这个时候就有两个`Bean`对象，可以通过容器管理了。

其次呢，`Spring`容器还可以管理`bean`的声明周期，实现一些业务逻辑，那我们重新再写一个`Bean`

`AlphaService`：

```java
package com.nowcoder.community.service;

import com.nowcoder.community.dao.AlphaDao;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Service;

import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;

@Service
@Scope("prototype")//导致每次getbean都实例化很少会这样去做
public class AlphaService {

    @Autowired
    private AlphaDao alphaDao;

    //被Spring容器管理的bean只被实例化一次，因为它是单例的
    public AlphaService() {
        System.out.println("实例化AlphaService");
    }

    @PostConstruct//初始化在构造器之后
    public void init() {
        System.out.println("初始化AlphaService");
    }

    @PreDestroy//销毁之前调用，释放某些资源
    public void destroy() {
        System.out.println("销毁");
    }

    public String find() {
        return alphaDao.select();
    }
}

```

上面我们都是自己写的`Bean`，但是有的时候我们希望能在容器中加载一个第三方的`Bean`，

那我们就需要自己写一个配置类，在配置类中通过`Bean`注解进行申明，那么就开始写一个配置类。

所有第三方的都写在`config`这个包里面：

`AlphaConfig`：

```java
package com.nowcoder.community.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import java.text.SimpleDateFormat;

//配置类，加载第三方的bean
@Configuration
public class AlphaConfig {
    @Bean
    public SimpleDateFormat simpleDateFormat() {//方法名就是bean的名字
        return new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");

    }
}

```

但是这种都是自己去主动去获取，我们其实可以通过依赖注入来实现。

在使用之前进行申明就可以了，使用这个`@Autowired`注解。

上面都是`bean`声明，下面就是一个具体的测试的方法了。

`CommunityApplicationTests`：

```java
package com.nowcoder.community;

import com.nowcoder.community.dao.AlphaDao;
import com.nowcoder.community.service.AlphaService;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.BeansException;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.context.assertj.ApplicationContextAssertProvider;
import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringRunner;

import java.text.SimpleDateFormat;
import java.util.Date;

@RunWith(SpringRunner.class)
@SpringBootTest
@ContextConfiguration(classes = CommunityApplication.class)
public class CommunityApplicationTests implements ApplicationContextAware {

	private ApplicationContext applicationContext;

	@Override
	public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
		this.applicationContext = applicationContext;
	}

	@Test
	public void testApplicationContext() {
		System.out.println(applicationContext);//获取容器

		AlphaDao alphaDao = applicationContext.getBean(AlphaDao.class);//获取bean，通过类型获取bean
		System.out.println(alphaDao.select());

		alphaDao = applicationContext.getBean("alphahibernate",AlphaDao.class); //通过名字强制获取到这个bean，这个函数默认是返回对象，所以要加后面的这个类型，使用强制转换
		System.out.println(alphaDao.select());
	}

	@Test
	public void TestBeanManagement() {
		AlphaService alphaService = applicationContext.getBean(AlphaService.class);
		System.out.println(alphaService);

		alphaService = applicationContext.getBean(AlphaService.class);
		System.out.println(alphaService);
	}

	@Test
	public void TestBeanConfig() {
		SimpleDateFormat simpleDateFormat = applicationContext.getBean(SimpleDateFormat.class);
		System.out.println(simpleDateFormat.format(new Date()));
	}

	@Autowired
	@Qualifier("alphahibernate")//加载我们制定的bean
	private AlphaDao alphaDao;
	@Autowired
	private SimpleDateFormat simpleDateFormat;

	@Test
	public void TestDI() {
		System.out.println(alphaDao.select());
		System.out.println(simpleDateFormat);
	}
}
```

