### 1、Spring Boot是如何扩展Spring MVC的？

Spring Boot为Spring MVC提供了自动配置，可以让大多数应用程序与Spring Boot完美配合。

实现只需在类上添加`@Configuration`注解并且让该类实现`WebMvcConfigurer`接口，然后就可以使用我们的配置类了，

但是在之前的版本是继承WebMvcConfigurerAdapter来完成的，2.0中这个适配类被弃用了，改成直接实现**WebMvcConfigurer**来完成MVC的扩展。



### 2、聊聊IOC和AOP

Spring 提供两个核心技术IOC容器和AOP增强。

IOC控制反转：将对象的创建权交给了spring容器来管理，

AOP面向切面：将程序中的交叉业务逻辑（比如安全，日志，事务），封装成一个切面，然后注入到目标业务逻辑中去。（其实就是把公用代码封装起来，然后对目标对象动态地添加功能。）

*注：IOC就是控制反转(就是将原本由程序代码直接操作的对象的调用权交给容器)，目的是为了减低计算机代码的耦合度，也就是代码中的逻辑关系不要太紧密，避免后面改的人会因为不懂业务逻辑导致改错代码；除此之外也避免我们每次创建新的对象，减少对应的代码量。*



### 3、Spring有什么缺点？

* <font color="red">集成第三方工具时候，还要考虑工具之间的一个兼容性。</font>而使用Spring Boot它都会帮我们管理，所以不用过多的担心版本兼容问题。
* <font color="red">不具备热部署功能，完全依赖虚拟机或者 Web 服务器的热部署</font>。而Spring Boot
* 使用门槛升高，要入门 Spring 需要较长时间。
* XML 配置已经不是流行的系统配置方式 。
* 大量繁琐的配置，导致使用复杂度升高

### 4、Spring Boot优点

* 使开发者能够快速开发一个Web应用，开箱即用
* 不需要配置就能运行Spring应用，不管我们构建简单或者复杂系统，只需要少量配置及代码就能完成。
* 有大量的自动配置，简化开发和集成大量地三方工具
* 提供了内置的 Tomcat 或者 Jetty 容器 

### 5、你知道Spring Boot有几种热部署吗？

我目前经常使用的有前2种热部署方式

1. 模板引擎：页面模板改变ctrl+F9可以重新编译当前页面并生效
2. 使用devtools工具包，操作简单，但是每次需要重新部署
3. 使用springloaded本地加载启动，配置jvm参数

其实还有一种就是JRebel可以实现热部署，但可以是收费

### 6、简单介绍在Spring你常用的注解

Spring提供了很多个注解，最最常用有注解**@Bean、@Controller、@RequestMapping、@Component、@Configuration、Aspect、Around、simpleAop**

* bean就相当于定义一个组件，这个组件是用于具体实现某个功能的
* 如在类上声明@Component , @Repository , @ Controller , @Service , @Configration这些注解都是把你要实例化的对象转化成一个Bean，放在IoC容器中，等你要用的时候，直接拿来用即可。（注册Bean）
* Component：声明此类是一个Spring管理的类
* @Configuration：声明此类是一个配置类，通常与注解＠Bean 配合使用
* Controller：声明此类是一个 MVC 类， 通常与＠Reques tMapping 一起使用
* @Aspect，声明了这是一个切面类 
* @Around，声明了 一个表达式，描述要织入的目标的特性
* simpleAop 是用来织入的代码 

### 7、springBoot和SpringMVC的区别

* SpringBoot约定大于配置，降低了项目搭建的难度，从而简化spring的配置流程

* springMVC需要使用到TomCat服务器，SpringBoot的话是内嵌了Tomcat服务器的
* springMVC是一个Web框架，提供了一个轻度耦合的方式来开发Web应用的。通过DispatcherServelet，MoudlAndView 和 ViewResolver 等一些简单的概念，让开发 Web 应用将会变更加简单
* 对于Spring 和 SpringMVC 的问题在于需要配置大量的参数，然而使用Spring Boot开发就通过一个自动配置和启动就能解决。

### 8、RequestMapping 和 GetMapping 的不同之处在哪里？

RequestMapping 具有类属性的，可以进行 GET,POST,PUT。而GetMapping 是 GET 请求方法中的一个特例，它只是 ResquestMapping 的一个延伸，目的是为了提高清晰度。

### 9、SpringBoot自动装配原理

从@SpringBootApplication启动注解入手

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {...}
```

**其中重要的只有三个Annotation：@SpringBootConfiguration（实质就是一个@Configuration）、@EnableAutoConfiguration、@ComponentScan**

也就是说在开发的时，如果加上上面的三个注解就等同于加上@SpringBootApplication注解

1. @Configuration注解：如果在类上使用该注解就是代表了一个配置类，就相当于之前使用Spring时的beans.xml文件。

2. @ComponentScan注解：该注解功能其实就是自动扫描并加载符合条件的组件或bean定义，最终将这些bean定义加载到容器中。（组件扫描）

3. @EnableAutoConfiguration注解：标注该注解代表开启springboot的自动装配。(**重点**)

   * ```java
     @Target(ElementType.TYPE)
     @Retention(RetentionPolicy.RUNTIME)
     @Documented
     @Inherited
     @AutoConfigurationPackage
     @Import(AutoConfigurationImportSelector.class)
     public @interface EnableAutoConfiguration {
     	String ENABLED_OVERRIDE_PROPERTY = "spring.boot.enableautoconfiguration";
     	Class<?>[] exclude() default {};
     	String[] excludeName() default {};
     
     }
     ```

   * 从源码看有很多的注解，最关键就是`自动导入选择器：@Import(AutoConfigurationImportSelector.class)`，它是帮助Spring Boot应用将所有符合条件的`@Configuration`配置都加载到当前Spring Boot创建并使用的IoC容器中的。同时借助Spring框架原有的一个工具类`SpringFactoriesLoader`的，从而`@EnableAutoConfiguration`就可以实现智能的自动配置了，它就是这么爽！人家都说了嘛，想要成功就要学会站在巨人的肩膀上去努力。

总结：

1. 通过各种注解实现了类与类之间的依赖关系，启动Spring Boot时候，会调用自动导入选择器`EnableAutoConfigurationImportSelector.class`的导入选择`selectImports`的方法。

2. selectImports方法最终会调用SpringFactoriesLoader.loadFactoryNames方法来获取一个全面的常用BeanConfiguration列表

3. loadFactoryNames方法它会读取（spring-boot-autoconfigure-2.3.0.RELEASE.jar下的spring.factories）来获取所有的Spring相关的Bean的权限定名ClassName（看了源码大概有120多个）

4. 然后通过过滤器来筛选出符合条件的依赖

5. 最后把符合条件的注入到IOC环境当中

   

### 上方

\> Show Execution Point (Alt + F10)：如果你的光标在其它行或其它页面，点击这个按钮可跳转到当前代码执行的行。

　　　　> Step Over (F8)：步过，一行一行地往下走，如果这一行上有方法不会进入方法。

　　　　> Step Into (F7)：步入，如果当前行有方法，可以进入方法内部，一般用于进入自定义方法内，不会进入官方类库的方法，如第25行的put方法。

　　　　> Force Step Into (Alt + Shift + F7)：强制步入，能进入任何方法，查看底层源码的时候可以用这个进入官方类库的方法。

　　　　> Step Out (Shift + F8)：步出，从步入的方法内退出到方法调用处，此时方法已执行完毕，只是还没有完成赋值。

　　　　> Drop Frame (默认无)：回退断点，后面章节详细说明。

　　　　> Run to Cursor (Alt + F9)：运行到光标处，你可以将光标定位到你需要查看的那一行，然后使用这个功能，代码会运行至光标行，而不需要打断点。

　　　　> Evaluate Expression (Alt + F8)：计算表达式，后面章节详细说明。

### 左侧

\> Rerun 'xxxx'：重新运行程序，会关闭服务后重新启动程序。

　　　　> Update 'tech' application (Ctrl + F5)：更新程序，一般在你的代码有改动后可执行这个功能。而这个功能对应的操作则是在服务配置里，如图2.3。

　　　　> Resume Program (F9)：恢复程序，比如，你在第20行和25行有两个断点，当前运行至第20行，按F9，则运行到下一个断点(即第25行)，再按F9，则运行完整个流程，因为后面已经没有断点了。

　　　　> Pause Program：暂停程序，启用Debug。目前没发现具体用法。

　　　　> Stop 'xxx' (Ctrl + F2)：连续按两下，关闭程序。有时候你会发现关闭服务再启动时，报端口被占用，这是因为没完全关闭服务的原因，你就需要查杀所有JVM进程了。

　　　　> View Breakpoints (Ctrl + Shift + F8)：查看所有断点，后面章节会涉及到。

　　　　> Mute Breakpoints：哑的断点，选择这个后，所有断点变为灰色，断点失效，按F9则可以直接运行完程序。再次点击，断点变为红色，有效。如果只想使某一个断点失效，可以在断点上右键取消Enabled，如图2.4，则该行断点失效。







第一件事：

* Java基础、Java集合、多线程高并发、JVM、计算机网络

* 熟悉使用Spring Boot和Mysql。掌握Redis、zookeeper

第二件事：搞一个开源圈子。及把英语和高等数学提升一下

第三件事：等毕业时拿出属于自己的最好的项目去面试（或者期间多参加一些算法大赛或者相关编程比赛）







