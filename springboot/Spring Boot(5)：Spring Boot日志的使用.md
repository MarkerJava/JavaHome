## Spring Boot日志介绍

Spring Boot底层使用的的日志框架为SLF4j（日志的抽象层）、logback（日志实现）

SLF4j的使用，在代码中

```java
Logger logger = LoggerFactory.getLogger(getClass());
```

然后导入对应的jar包

## 统一使用日志SLF4j

导入日志依赖

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-logging</artifactId>
  <version>2.2.1.RELEASE</version>
  <scope>compile</scope>
</dependency>
```

## Spring Boot 日志使用

```java
  //创建一个记录器对象
Logger logger = LoggerFactory.getLogger(getClass());

@Test
public void testLog(){
    /**
     * 日志级别，由高到低输出（debug<info<warn<error）
     * 可调整日志级别，按照需要打印日志
     * springboot 默认是info级别的，即运行代码，只会打印info
     * 以上级别日志
     */
    logger.debug("debug调试日志");
    logger.info("需要打出的信息info日志");
    logger.warn("警告warn日志");
    logger.error("错误的error日志");
}
```

## 设置日志打印级别

我们需要在配置文件yml中来设置，这样就会按照设置的级别来打印

```yml
logging:
  level:
    com.example: debug
```

## 将日志打印到文件中

```yml
logging:
  level:
    com.example: debug
  file:
    name: myLog.log
```

