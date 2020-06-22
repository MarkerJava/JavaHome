*上一篇是Spring Boot快速入门：[Spring Boot(1)：快速入门及自动配置源码剖析]()，如果还不接触过，建议先去看看上一篇文章。本章接着上一篇文章继续讲解Spring Boot Web开发，也是相当web的综合开发。*

<font color="red">本文使用thymeleaf模版引擎，在以后的文章中同样也是使用thymeleaf模版引擎。</font>

## 一、Spring Boot 集成MVC框架

### 1、引入依赖

Spring Boot 集成 Spring MVC 框架并实现自动配置，只需要在 porn 中添加以下依赖即可， 不需要其他任何配置。

```java
				<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
          <!--Thymeleaf模版引擎-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>  
```

2、项目结构

### 2、Web 应用目录结构

Web 的模板文件位于` resources/templates `目录下，模板文件使用的静态资源文件，**如: JS 、 css 、图片，存放在 resources/static 目录下** 。 在 MVC 中， 视图名自动在 templates 目录下找到对应的模板名称，模板中使用的静态资源将在 static 目录下查找 。

### 3、编写controller层

使用 Spring MVC 只需要在类上声明＠Controller， 标注这是一个 Controller 即可。 对于用户请求， 使用＠RequestMapping 映射 HTTP 请求到特定的方法处理类 。

```java
@Controller
public class UserController {
    @RequestMapping("/hello")
    public String test(Model model) {
        model.addAttribute("name","MarkerJava");
        model.addAttribute("user", Arrays.asList("merk","java"));
        return "test";
    }
}
```

**对以上代码的注解进行剖析**

* @Controller 作用于类上 ，表示这是一个 MVC 中的 Controller。

* @RequestMapping 可以作用在`方法或类上`。如上面代码，如果用户访问`/hello`，然后它会交由UserController.test方法来处理。stest() 方法返回的类型是字符串，默认是视图的名称 。 Spring Boot 的视图默认保存在` resources/templates` 目录下 ， 因 此， 渲染的视图是`／resources/templates/test.html `模板文件。

* 如果想直接返回内容而不是视图名 ，而是 JSON 字符串，则需要在方法上使用`＠ResponseBody`即可。ResponseBody 注解直接将返回的对象输出到客户端， 如果是字符串 ，则默认使用 Jackson 序列化成 JSON 字符串后输出 。

  * ```java
    @Controller
    public class UserController {
        @RequestMapping("/hello")
      	@ResponseBody
        public String test() {
            return "test";
        }
    }
    ```

  * *<font color="red">这时候他就会返回test字符串，而不是html页面。ResponseBody 注解直接将返回 的对象输出到客户端， 如果是字符串 ， 是 ，则默认使用 Jackson 序列化成 JSON 字符串后输出 。 </font>*

### 4、编写一个test.html页面

页面保存在` resources/templates` 目录下

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
  <p>微信搜索：MarkerJava</p>
</body>
</html>
```

这时候，打开浏览器访问默认端口`localhost:8080/hello`即可访问到html页面的内容：`微信搜索：MarkerJava`了。

## 二、controller层方法的参数

在学习Spring 的时候 Controller 方法可以接受多种类型参数，比如path变量，以及MVC的Model等，除此之外，方法还能接受更多的参数如下。

|                   方法参数                    |                             作用                             |
| :-------------------------------------------: | :----------------------------------------------------------: |
|    <font color="red">@Path Variable</font>    |          用于从请求URL中获取参数井映射到方法参数中           |
| <font color="red">Model & ModelAndView</font> |        渲染视图的模型  & 包含了模型和视图路 径的对象         |
|                 @RequestParam                 |      对应于 HTTP 请求的参数， 自动转化为参数对应 的类型      |
|                @RequestHeader                 |        对应于 HTTP 请求头参数， 自动转化为对应的类型         |
|                 MultipartFile                 |                     用于处理文件上传 。                      |
|    <font color="red">@RequestBody </font>     | 自动将请求内容转为指定的对象，默认使用 HtthMessageConverte 来转化 |
|                 @RequestPart                  |    用于文件上传， 对应于 HTTP 协议的 multipart/form-data     |
|               @SessionAttribute               |            该方法标注的变量来 自于 Session 的属性            |
|               @RequestAttribute               |              该标注的变量来自于 request 的属性               |
|            BindingResult 和 Errors            |                 用来处理绑定过程 中的错误。                  |
|                  HttpMethod                   |         枚举类型，对应于 HTTP Method，如 POST 、GET.         |

**对常用部分参数进行说明**

1. **Model & ModelAndView**
   * Model addAttribute(String attributeName, Object attributeValue） ，向模型添加一个变量， attributeN ame 指明了变量的名称， 可以在随后的视图里引用， attributeValue 代表了变量。
   * Model addAttribute(Object attribute Value），向模型添加一个变量， 变量的名字就是其类名字首字母小写后转为的Java 变量 。

   * Model addAllAttributes(Map at位ibutes）， 添加多个变量， 如果变量已经存在， 则覆盖。
2. **＠RequestParam注解的属性**
   * value ，指明 HTTP 参数的名称
   * required，400 错误 。boolean 类型， 声明此参数是否必须
   * defaultValue ， 字符类型， 如果 HTTP 参数没有提供， 可以指定一个默认字符串， 类型转化为目标类型， 如上一个例子， 我们可以提供默认参数。
3. **@ RequsetBody**
   * Controller 方法带有＠RequsetBody 注解的参数 ， 意味着请求的 HTTP 消息体的内容是一个 JSON， 需要转化为注解指定 的参数类型。Spring Boot 默认使用 Jackson 来处理反序列化工作。
4. **MultipartFile处理文件上传**
   * getOriginalF ile name：获取上传的文件名字 
   * getBytes ：我取上传文件内容，转为字节数组
   * getlnputStream：获取一个 InputStream
   * isEmpty：文件上传内容为空 ，或者就没有文件上传 
   * getSize：获取文件大小
   * transferToσile dest：保存上传文件到目标文件系统 

## 三、JSR-303

*JSR-303 是 Java 标准的验证框架*

|        注解         |                   作用                   |
| :-----------------: | :--------------------------------------: |
|        @Null        |             验证对象是否为空             |
|      @NotNull       |              验证对象不为空              |
|      @NotBlank      |     验证字符串不为空或者不是空字符串     |
|      @NotEmpty      |    验证对象不为 null， 或者集合不为空    |
| @Size(min=, max＝） |    验证对象长度， 可支持字符串、集合     |
|       @Length       |                字符串大小                |
|        @Min         |       验证数字是否大于等于指定的值       |
|        @Max         |       验证数字是否小于等于指定的值       |
|       @Digits       |         验证数字是否符合指定格式         |
|       ＠Range       |        验证数字是否在指定的范围内        |
|       @Email        | 验证是否为邮件格式， 为 null 则不做校验  |
|     **@Patten**     | 验证 String 对象是否符合正则表达式的规则 |



## 四、扩展Spring MVC使用@Configuration注解

将该类变成一个配置类，WebMvcConfigurer是用来全局定制化Spring Boot的MVC特性，我们可以通过实现WebMvcConfigurer 接口来配置应用的 MVC 全局特性。

使用一般新建一个包config与controller同级的，然后编写一个类，且在该类上使用`@Configuration`。**意思就是将该类变成一个配置类，代码如下。**

```java
//将该类变成一个配置类
@Configuration
public class MyMvcConfig implements WebMvcConfigurer {
    //实现了视图解析器接口的类，我们把它看作视图解析器
    @Bean
    public ViewResolver myViewResolver() {
        return new MyViewResolve();
    }
    public static class MyViewResolve implements ViewResolver {
        @Override
        public View resolveViewName(String viewName, Locale locale) throws Exception {
            return null;
        }
    }
    //URI到视图的映射
    //视图跳转,打开浏览器访问.../markjava，然后就可以跳转到test.html页面
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/markjava").setViewName("test");
    }
  ／／拦截器
  public void addinterceptors(InterceptorRegistry registry){}
  ／／跨域访问配置
  public void addCorsRegistry(CorsRegistry registry){}
  ／／格式化
  public void addFormatterRegistry(FormatterRegistry registry){}
```

*注：在Spring Boot1.0的时候可以通过继承**WebMvcConfigurerAdapter**完成，但在Spring Boot2.0中这个适配类是被弃用了的，所以我们可以直接实现**WebMvcConfigurer**来完成拦截器的添加*

## 全面接管SpringMVC

SpringBoot对SpringMVC的自动配置不需要了，所有都是我们自己配置；所有的SpringMVC的自动配置都失效了 我们需要在配置类中添加@EnableWebMvc即可。

```java
//使用WebMvcConfigurerAdapter可以来扩展SpringMVC的功能 
@EnableWebMvc 
@Configuration 
public class MyMvcConfig extends WebMvcConfigurerAdapter {

@Override public void addViewControllers(ViewControllerRegistry registry) {
// super.addViewControllers(registry);
//浏览器发送 /atguigu 请求来到 success
registry.addViewController("/atguigu").setViewName("success"); }}
```

## 自定义视图解析器

```java
@Configuration
public class MvcConfig  extends WebMvcConfigurerAdapter {
 
    @Bean
    public ViewResolver myViewResolver(){
        return new MyViewResolver();
    }
}
public class MyViewResolver implements ViewResolver {
	@Override
	public View resolveViewName(String viewName, Locale locale) throws Exception {
		return null;
	}
}
```



## 通用错误处理

在 Spring Boot 中， c。 由oiler 中抛出的异常默认交给 了／error 来处理， 应用程序可以将／error 映射 到一个特定的 Controller 中处理来代替 Spring Boot 的默认实现， 应用可以继承 AbstractEirnr℃ontrolle r 来统一处理系统的各种 异常。

```java
package com.mark.controller;

import org.springframework.stereotype.Controller;
import org.springframework.util.StringUtils;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RequestParam;

import javax.servlet.http.HttpSession;
import java.util.Map;

@Controller
public class LoginController {

    //方法处理登录,@RequestMapping处理请求（当前项目下的user/login）method以post请求
    //写得有点长，不利于可读性可以使用@PostMapping注解、Get、put及DeleteMapping都是可以的
    //使用@RequestParam限定，让它从请求参数里面获取用户名和密码，如果用户没有提交就会报错
    //value = "/user/login"以及username等是html页面的<th:action="@{/user/login}" method="post">
    @RequestMapping(value = "/user/login",method = RequestMethod.POST)
    public String login(@RequestParam("username") String username,
                        @RequestParam("password") String password,
                        Map<String,Object> map, HttpSession session){
        //判断username不为空及密码,则登录成功，就跳转到相应页面（return "dashboard"）
        if(!StringUtils.isEmpty(username) && "123456".equals(password)) {
            //登录成功需要跳转的页面,防止表单重复提交，可以重定向主页
            session.setAttribute("loginUser",username);//意思是只要登录啦，就在session中存在
            return "dashboard:/main.html";
        } else {
            //登录失败,会跳转到登录页面
            return "login";
        }
    }
}

```

```java
package com.mark.config;

import org.springframework.web.servlet.HandlerInterceptor;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class LoginHandlerIntreceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        //登录成功之后，应该有用户的session
        Object loginUser = request.getAttribute("loginUser");
        if (loginUser == null){//用户没有登录直接回到登录页面
            request.setAttribute("msg","没有登录");
            request.getRequestDispatcher("/index.html").forward(request,response);
            return false;
        }else {
            return true;
        }
    }
}

```

```java
package com.mark.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.*;

//@EnableWebMvc 全面接管Spring MVC（Spring MVC自动配置全部失效）
//@EnableTransactionManagement 开启事务支持后
@Configuration
public class MyMvcConfig implements WebMvcConfigurer {
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/").setViewName("login");
        registry.addViewController("index.html").setViewName("login");
        //重定向，防止表单重复提交（但是还是有问题）
        registry.addViewController("/main.html").setViewName("dashboard");
    }

    /**
     * 1、addInterceptor用于添加你自定义的拦截器实例
     * 2、addPathPatterns用于添加要拦截的url,可以写多个
     * 3、excludePathPatterns用于添加不需要拦截的url,可以写多个
     * 4、例如excludePathPatterns("/","user/login","index.html")：登录页面、首页、登录请求等
     * @param registry
     */
    
}
```



## 参考

Spring Boot 2精髓从构建小系统到架构分布式大系统.李家智（书籍）
