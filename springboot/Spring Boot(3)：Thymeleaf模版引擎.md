## 一、什么是Thymeleaf

Thymeleaf 是一款用于渲染 XML/XHTML/HTML5 内容的模板引擎。类似 JSP，Velocity，FreeMaker 等模版，它也可以轻易的与 Spring MVC 等 Web 框架进行集成作为 Web 应用的模板引擎。可以在Web和非Web环境中工作。 它更适合在基于MVC的Web应用程序的视图层提供XHTML / HTML5，但它甚至可以在脱机环境中处理任何XML文件。 它提供完整的Spring Framework。

与其它模板引擎相比，Thymeleaf 最大的特点是能够直接在浏览器中打开并正确显示模板页面，而不需要启动整个 Web 应用。*Thymeleaf 也是Spring Boot官方推荐使用模版引擎来代替 Jsp的，同时也是未来的一个趋势。*

学习thymeleaf需要Spring Boot基础，请参考：[Spring Boot(1)：入门篇](https://blog.csdn.net/realize_dream/article/details/106528329)

## 二、Thymeleaf快速入门

*接下来我们来做一个小 demo*

<font color="red">**步骤1**：</font>在使用Thymeleaf之前，首先在pom.xml文件导入它的坐标

```java
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

*注：为什么没有导入版本？版本号是交由spring-boot-start-parent来管理了。*

<font color="red">**步骤2**：</font>在`resource`目录下的templates目录新建一个html页面`test.html`（另外Thymeleaf默认的页面文件后缀是`.html`）。

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<!--/*@thymesVar id="name" type="com.markjava.controller"*/-->
<h1 th:text="${name}"></h1>

</body>
</html>
```

<font color="red">**步骤3**：</font>编写controller层

```java
package com.markjava.controller;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
@Controller
public class UserController {

    @RequestMapping("/hello")
    public String test(Model model) {
        model.addAttribute("name","MarkerJava");
        return "test";
    }
}
```

然后打开浏览器访问`localhost:8080/hello`运行：会获取到`MarkerJava`

**解释以上内容**

* 首先要使用thymeleaf模版引擎，在html页面导入：`<html xmlns:th="http://www.thymeleaf.org">`，xmlns:th指的是将thymeleaf的命名空间的名字命名为th，这个符号和之后所用的th一致
* `th:text`：在th后面加上一个冒号，并附加特定的字符组合，这个thymeleaf定义的占位符是构建thymeleaf页面的基础。
* `th:text="${name}"`中的`${}`是个占位符

## 三、设置热部署

虽然现在可以正常运行，但是每次我们修改代码后需要重启服务，在实际开发中如果项目很大，重启是需要时间的，所以我们一起来设置自动编译，这样就不用每次重启服务了。

**修改pom.xml文件添加一下内容**

```java
       <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
```



```java
        <plugin>
					<groupId>org.springframework.boot</groupId>
					<artifactId>spring-boot-maven-plugin</artifactId>
					<configuration>
						<fork>true</fork>
					</configuration>
				</plugin>
```

设置IDEA中：打开IDEA—>【settings】—>【Compiler】—>勾选【Bulid project automatically】即可。

## 四、Thymeleaf语法

*经过上面的入门案例，相信大家都觉得Thymeleaf很简单吧，接下学习在实际开发中常用的语法。*

### 1、Thymeleaf标准表达式语法分为四类

* 变量表达式`${ }`

```html
<!--页面中使用变量表达式${}来获取-->
<p th:utext="${user.name}"></p>
<h2 th:each="user:${user}" th:text="${user}"></h2>
```

* 选择或星号表达式`*{ }`

```html
<!--选择表达式的使用方法如下所示-->
<div th:object="${book}">  
  <span th:text="*{title}">...</span>    
</div>  
```

* 文字国际化表达式`#{}`

```html
<table>  
  <th th:text="#{header.address.city}">...</th>  
  <th th:text="#{header.address.country}">...</th>  
</table>  
```

* URL 表达式`@{ }`

```html
<!--URL 表达式指的是把一个有用的上下文或回话信息添加到 URL，这个过程经常被叫做 URL 重写。 -->
<!--也就是URL链接表达式会给URL自动添加上下文的名字-->
<a th:href="@{/markjava}">markjava</a>
```

如果要在URL中传递参数，如http://localhost:8080/hello/markjava?name=ha，可以这样操作

```html
<a th:href="@{/markjava(name=${session.user.name})}">markjava</a>
<!--传递多个参数-->
<a th:href="@{/markjava(name=${session.user.name},age=${session.user.age})}">markjava</a>
```



## 参考

[thymeleaf官方文档](https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html)

[纯洁的微笑：Spring Boot(四)：Thymeleaf 使用详解](http://www.ityouknow.com/springboot/2016/05/01/spring-boot-thymeleaf.html)

