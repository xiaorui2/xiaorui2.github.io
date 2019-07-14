---
title: SpringBoot的一些面试题
date: 2019-06-30 17:43:29
tags: SpringBoot
categories: SpringBoot项目
---

# SpringBoot的IOC和AOP

## IOC

它是一个容器的感觉,听过最多的一个词：控制反转，它表示让容器管理对象，不用每次都自己取new对象。使用`@Service`和`@Autowired`提供和使用服务。`spring` 是一种基于`IOC`容器编程的框架。 `spring` 把每一个需要管理的对象称为`spring bean`，`spring`管理这些`bean` 被我们称之为`spring ioc `容器。`IOC`容器具备两个基本功能：

- 通过描述管理（发布，获取）`bean`

- 通过描述完成 `bean`之间的依赖关系

一个对象的实例和字段的值被一个特殊的对象从外部注入，这个特殊的对象就是`IOC`。

`IOC`容器包含了所有的`Spring Beans`。

## `AOP`

切面监控，面向切面编程，可以监控任何文件，目前普遍用于日志。这是基本的，它是基于代理模式实现的。

### 代理模式

定义：为其他对象提供一种代理以控制对这个对象的访问。这段话比较官方，但我更倾向于用自己的语言理解：比如`A`对象要做一件事情，在没有代理前，自己来做，在对`A`代理后，由`A`的代理类`B`来做。代理其实是在原实例前后加了一层处理，这也是`AOP`的初级轮廓。

代理的话又分为：

- 静态代理模式：静态代理说白了就是在程序运行前就已经存在代理类的字节码文件，代理类和原始类的关系在运行前就已经确定，保证了业务类只需关注逻辑本身，但是如果要代理的方法很多，代码就很复杂了。
- 动态代理模式:动态代理类的源码是在程序运行期间通过`JVM`反射等机制动态生成，代理类和委托类的关系是运行时才确定的。

### 动态代理又有两种方法：

- 使用`jdk`生成的动态代理的前提是目标类必须有实现的接口。但这里又引入一个问题,如果某个类没有实现接口,就不能使用`jdk`动态代理
- `Cglib`是以动态生成的子类继承目标的方式实现，在运行期动态的在内存中构建一个子类，`Cglib`使用的前提是目标类不能为`final`修饰。因为`final`修饰的类不能被继承。

### Spring生成代理对象

- 创建容器对象的时候，根据切入点表达式拦截的类，生成代理对象。
- 如果目标对象有实现接口，使用`jdk`代理。如果目标对象没有实现接口，则使用`cglib`代理。然后从容器获取代理后的对象，在运行期植入"切面"类的方法。

# 注解的作用

## @SpringBootApplication

表示这是一个配置文件，点击进去可以看到这些配置文件

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration //配置文件
@EnableAutoConfiguration //自动配置
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })// 组件扫描，扫描配置类和子包下的bean
```

## @SpringBootTest配合@ContextConfiguration(classes = CommunityApplication.class)

引入的一个用于测试的注解

## @Component

泛指组件，把普通`pojo`实例化到`spring`容器中，相当于配置文件中的  `<bean id="" class=""/>`。

因为在持久层、业务层和控制层中，分别采用`@Repository`、`@Service`和`@Controller`对分层中的类进行凝视，而用`@Component`对那些比较中立的类进行凝视。

## @Controller

用于标注控制层，相当于`struts`中的`action`层

## @Autowired

用来做依赖注入的，直接生成就不用`new`对象了。

## 	@Service

用于标注服务层，主要用来进行业务的逻辑处理

### @PostConstruct

修饰的方法在构造器之后被调用

### @PreDestroy

修饰的方法在销毁之前调用，释放某些资源

## @Repository

用于标注数据访问层，也可以说用于标注数据访问组件，即`DAO`组件.

## @Configuration

`@Configuration`定义配置类，被注解的类内部包含有一个或多个被`@Bean`注解的方法,这些方法将会被`AnnotationConfigApplicationContext`或`AnnotationConfigWebApplicationContext`类进行扫描，并用于构建`bean`定义，初始化`Spring`容器。

## @Bean

`@Bean`注解注册`bean`,同时可以指定初始化和销毁方法

## @Scope("prototype")

这个注解导致每次调用`getbean`方法时都实例化`bean`，但是实际上很少会这样去做。记住被`Spring`容器管理的`Bean`只被实例化一次，因为它是单例的。

# 哪些bean会被扫描

被`@controller` 、`@service`、`@repository` 、`@component `注解的类，都会把这些类纳入进`spring`容器中进行管理

# Spring容器管理Bean

容器实现了`IOC`，

`Bean`的实例化；`Bean`的命名；`Bean`的作用域；`Bean`的生命周期回调；`Bean`延迟实例化；指定`Bean`依赖关系。

## Bean的生命周期

`Spring IOC`容器对`Bean`的生命周期进行管理的过程如下：

- 通过构造器或工厂方法创建`Bean`实例
- 为`Bean`的属性设置值和对其它`Bean`的引用
- 调用`Bean`的初始化方法
- `Bean`可以使用了
- 当容器关闭时，调用`Bean`的销毁方法

# Spring Boot 需要独立的容器运行吗？

可以不需要，内置了 `Tomcat/ Jetty `等容器。