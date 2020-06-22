## Profile

*在Spring Boot中Profile是Spring Boot用来对**不同环境或者指令**来读取不同的配置文件。*

### 1、Profile使用场景

比如在开发中，有开发、测试、生产三个不同的环境，需要定义三个不同环境下的配置的这样场景，这时候你就可以在resource目录下定义不同环境配置了。

<font color="red">**以applcation.properties为主配置文件**</font>

```xml
<!--以applcation.properties为主配置文件-->
applcation.properties
<!--开发环境-->
application-dev.properties
<!--测试环境-->
application-test.properties
<!--生产环境-->
application-prod.properties
```

假如`application-dev.properties`开发环境端口号是：`server.port=8082`我们是以applcation.properties为主配置文件的，所以在applcation.properties中，使用`spring.profiles.active=dev`激活开发环境即可使用如下

```mxl
# 更改端口号
server.port=8081

# 激活开发环境
spring.profiles.active=dev
```

打开web浏览器，访问localhost:8082即可。

<font color="red">**以applcation.yml为主配置文件**</font>

新建applcation.yml即可编写，不像以applcation.properties为主配置文件时需要创建生产、开发、测试。

```xml
server:
  port: 8082

#激活环境
spring:
  profiles:
    active: test
---
#开发环境
server:
  port: 8083
spring:
  profiles: dev

---
#生产环境
server:
  port: 8084
spring:
  profiles: prod

---
#测试环境
server:
  port: 8085
spring:
  profiles: test
```

<font color="red">**也可以同时激活三个配置**</font>

```xml
spring.profiles.active: prod,prod,test
```

