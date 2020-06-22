> 文章内容使用 Spring Boot 2.x

![](https://i.loli.net/2020/06/03/KYeENV5OjStv1UX.png)

## 一、什么是Spring Boot

[Spring Boot](https://spring.io/projects/spring-boot#learn)是基于Spring开发的，是约定大于配置的核心思想。并且集成了大量的第三方库配置比如redis、mongoDB、jpa等。Spring Boot就相当于maven整合了所有jar包，Spring Boot整合了所有框架。其设计目的是用来简化新 Spring 应用的初始搭建以及开发过程，并不少什么新的框架。

## 二、Spring Boot优点

**优点其实就是简单、快速**

* 快速创建独立运行的Spring项目以及主流框架集成
* 使用嵌入式的server容器，应用无需打成WAR包
* starters自动依赖与版本控制
* 有大量的自动配置，简化开发
* 准生产环境运行应用监控
* 与云计算的天然集成

**之前不使用Spring Boot搭建web项目的过程是有多么繁琐**

（1）配置 web.xml，加载 Spring 和 Spring mvc

（2）配置数据库连接、配置 Spring 事务、配置日志文件

（3）配置加载配置文件的读取，开启注解

（4）…

（5）配置完成之后部署 Tomcat 调试

## 三、Spring Boot快速玩转

*Spring Boot到底有多诱人，相信大家都迫不及待了，赶紧撸一把试试手！*

### 步骤一：新建项目

打开IDEA，新建项目，选择【Spring Initializr】 其他默认【Next】:

![](https://i.loli.net/2020/06/03/oxNcOZae6qdEsJb.png)

修改完【next】

![](https://i.loli.net/2020/06/03/kLOaop3iJrvz8nX.png)

勾选web模版

![](https://i.loli.net/2020/06/03/YTQGf12tDcqBjAh.png)

根据自己将项目存放路径，然后【finish】

![](https://i.loli.net/2020/06/03/bqSUROJg28ByAX6.png)

这样项目就搭建完成，Spring Boot目录结构如下

![](https://i.loli.net/2020/06/03/jt5JTE7GVxManXH.png)

### 步骤二：编写controller层

*建议：首先大家，在开发或者学习中，搭建好一个项目，先测试一下，看是否能跑起来。Spring Boot我们先来测试控制器，是否真的可以。*

![](https://i.loli.net/2020/06/03/U5zMiBcVwK87gus.png)

编写一个userCotroller类代码如下：

```java
package com.markjava.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class UserController {

    @GetMapping("/hello")
    @ResponseBody
    public String test() {
        return "hello spring boot";
    }
}
```

### 步骤三：启动项目

![](https://i.loli.net/2020/06/03/LrcH1kY2Mylotp9.png)

出现一下，说明你成功跑一个demo项目了

![](https://i.loli.net/2020/06/03/CqJFxfD4RUQ1kEp.png)

打开浏览器访问输入 `http://localhost:8080/hello`，效果就出来啦，是不是简单。

![](https://i.loli.net/2020/06/03/geLZ5pQ9xwOJvkU.png)

## 如何修改端口号

在`resources`目录下的`application.properties`添加或者``

![](https://i.loli.net/2020/06/03/eKiSTjWLZVtO4nG.png)

再次，打开浏览器访问输入 `http://localhost:8081/hello`

![](https://i.loli.net/2020/06/03/9rEa6BkLuHTNYMJ.png)

## Spring Boot热部署

其实，热部署在你构建项目的时候可以选择,或者通过`pom.xml`添加也是可以的，这样每次就不用都重启项目。

```java
 <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <optional>true</optional>
    </dependency>
```

## 四、分析Spring Boot目录结构及pom.xml

**基础结构共有3个 文件**

- `src/main/java` 程序开发以及主程序入口
- `src/main/resources` 配置文件
- `src/test/java` 测试程序

**pom.xml文件中核心模块**

```java
<! --该标签是在配置 Spring Boot 的父级依赖：-->
<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.0.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
 </parent>
  
  <dependencies>
  <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
   </dependency>
  </dependencies>
```

- `spring-boot-starter` ：核心模块，也是父级依赖，包括自动配置支持、日志和 YAML，如果引入了 `spring-boot-starter-web` web 模块可以去掉此配置，因为 `spring-boot-starter-web`自动依赖了 `spring-boot-starter`。
- `spring-boot-starter-test` ：测试模块，包括 JUnit、Hamcrest、Mockito。

---

<font color="red">*初学者，如果对源码有兴趣，请往下看会更精彩*</font>

## 五、主程序分析

````java
//@SpringBootApplication标注这个类是一个Springboot的应用
@SpringBootApplication
public class Springboot02DemoApplication {
    public static void main(String[] args) {
        //将Springboot应用启动
        SpringApplication.run(Springboot02DemoApplication.class, args);
    }

}
````

**SpringBootApplication源码剖析，进入源码，结果发现其实它是一个组合注解**

![](https://i.loli.net/2020/06/03/VJm84AXEK9jCZeB.png)

**第一个SpringBootConfiguration注解**：@SpringBootConfiguration-->是Spring Boot配置类。下面有一个叫@Configuration：它是配置类，下面又有@Component，其实它就是一个注入组件。

**第二个@EnableAutoConfiguration注解**：是开启自配配置功能

```java
@AutoConfigurationPackage//自动配置包
@Import(AutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {}
```

@AutoConfigurationPackage：自动配置包，使用@Import(AutoConfigurationImportSelector.class)注解来完成的，它是spring boot底层注解，作用是给容器中导入组件。

## 六、自动配置原理

（1）Spring Boot启动的时候首先加载主配置类，开启啦自动配置的功能`（@EnableAutoConfiguration）`

（2）`自动配置功能@EnableAutoConfiguration`的作用：它是利用了`@Import(AutoConfigurationImportSelector.class)`给容器中导入一些组件。那么，他会给我们导入哪些组件呢？进入`AutoConfigurationImportSelector`源码看一下部分源码如下：

```java
//@EnableAutoConfiguration注解
@AutoConfigurationPackage
@Import(AutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {}
```

---

1⃣️@Import(AutoConfigurationImportSelector.class)自动配置导入选择

```java
public class AutoConfigurationImportSelector implements DeferredImportSelector, BeanClassLoaderAware,
		ResourceLoaderAware, BeanFactoryAware, EnvironmentAware, Ordered {
      //----部分源码省略----//
protected AutoConfigurationEntry getAutoConfigurationEntry(AnnotationMetadata annotationMetadata) {
		if (!isEnabled(annotationMetadata)) {
			return EMPTY_ENTRY;
		}
		AnnotationAttributes attributes = getAttributes(annotationMetadata);
		List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes);
   //----部分源码省略----//
 }
```

`List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes);`是获取候选的配置，也就是获取所有配置。

**getCandidateConfigurations**//获取候选配置，核心方法

```java
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
  //SpringFactoriesLoader.loadFactoryNames通过加载器加载所有
		List<String> configurations = SpringFactoriesLoader.loadFactoryNames(getSpringFactoriesLoaderFactoryClass(),
				getBeanClassLoader());
		Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you "
				+ "are using a custom packaging, make sure that file is correct.");
		return configurations;
	}

//getBeanClassLoader()
protected ClassLoader getBeanClassLoader() {
		return this.beanClassLoader;
	}
//beanClassLoader
private ClassLoader beanClassLoader;
//下滑，找到
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
		List<String> configurations = SpringFactoriesLoader.loadFactoryNames(getSpringFactoriesLoaderFactoryClass(),
				getBeanClassLoader());
  	//断言不为空
		Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you "
				+ "are using a custom packaging, make sure that file is correct.");
		return configurations;
	}
注：META-INF/spring.factories是配置文件的核心
//getSpringFactoriesLoaderFactoryClass()
protected Class<?> getSpringFactoriesLoaderFactoryClass() {
		return EnableAutoConfiguration.class;//标注了这个类的所有包
	}
```

小结：将类路径下META-INF/spring.factories里面配置的所有EnableAutoConfiguration的值加到容器中

---

2⃣️@AutoConfigurationPackage自动配置包

```java
@Import(AutoConfigurationPackages.Registrar.class)//导入选择器，包注册
public @interface AutoConfigurationPackage {}
```

（3）每一个自动配置类进行自动配置功能

**总结**

（1）、SpringBoot启动会加载大量的自动配置类

（2）我们看我们需要的功能有没有SpringBoot默认写好的自动配置类；

（3）我们再来看这个自动配置类中到底配置了哪些组件；（只要我们要用的组件有，我们就不需要再来配置了）

（4）给容器中自动配置类添加组件的时候，会从properties类中获取某些属性。我们就可以在配置文件中指定这些属性的值；

xxxxAutoConfigurartion：自动配置类；

给容器中添加组件

xxxxProperties:封装配置文件中相关属性；

## 总结

使用 Spring Boot 可以非常方便、快速搭建项目，使我们不用关心框架之间的兼容性，适用版本等各种问题，我们想使用任何东西，仅仅添加一个配置就可以，所以使用 Spring Boot 非常适合构建微服务。（引自：[Spring Boot(一)：入门篇](http://www.ityouknow.com/springboot/2016/01/06/spring-boot-quick-start.html)）

## 参考资料

* 纯洁的微笑：[Spring Boot(一)：入门篇](http://www.ityouknow.com/springboot/2016/01/06/spring-boot-quick-start.html)

* 我没有三颗心脏；[Spring Boot 快速入门](https://www.cnblogs.com/wmyskxz/p/9010832.html)

* [Spring Boot实现支付服务：支付宝，微信…](https://gitee.com/52itstyle/spring-boot-pay)

- [Spring Boot后台商城 h5 小程序](https://gitee.com/JiaGou-XiaoGe/webappchat)
- [Vue+SpringBoot实现的多用户博客管理平台](https://github.com/lenve/VBlog)

>欢迎转载
>欢迎关注公众微信号：MarkerJava
>
><font color="red">下一篇：thymeleaf模版引擎</font>

![](https://i.loli.net/2020/06/03/9C3sWXHE217tMKS.jpg)

