# 一、Spring boot使用

> 使用yaml给实体类赋值

## 1、创建实体Dog

```java
@Component//该注解的意思就是加载到Spring Boot组件里面
ConfigurationProperties(prefix = "Dog")
public class Dog {
  //该注解表示赋值,但是我们并不是这样赋值，而是使用yaml进行
  //在yaml语法格式：Dog:
  								name: 旺旺
  								age: 3
  //写完yaml后，在该类上使用ConfigurationProperties(prefix = "Dog"),也就是和yaml绑定
  //@Value("汪汪")
  private String name;
  private String age;
}
```

## 2、再创建Person实体

```java
@Component//表示添加到Spring组件，否则Spring扫面不到
public class Person {
  private String name;
  private String age;
  private Map<String,Object> maps;
  private List<Object> lists;
  private Dog dog;
}
```

## 3、注入并使用，使用的注解@Autowired

> 使用application.properties给实体类赋值

### 1、创建实体Dog

```java
//指定配置文件
@PeopertSource(value = "classpath:xxx.xxx")
public class Dog {
  //该注解表示赋值,但是我们并不是这样赋值，而是使用yaml进行
  //在yaml语法格式：Dog:
  								name: 旺旺
  								age: 3
  //写完yaml后，在该类上使用ConfigurationProperties(prefix = "Dog"),也就是和yaml绑定
  @Value("${naem}")
  private String name;
  private String age;
}
```

### 2、再创建Person实体

```java
@Component//表示添加到Spring组件，否则Spring扫面不到
public class Person {
  private String name;
  private String age;
  private Map<String,Object> maps;
  private List<Object> lists;
  private Dog dog;
}
```

### 3、注入并使用，使用的注解@Autowired



## Spring基于注解开发（常用注解）

#### 1、创建maven工程

#### 2、导入Spring核心容器的依赖环境（包括context、aop、beans、core、logging、expression）

> 注：在Spring4之后，如果使用注解开发，必须要确保aop的包导入

#### 3、首先，项目工程完成（dao、controller、service、object）

##### 3.1 在object编写实体类User，开始使用注解

```java
//使用注解方式进行开发，在该类上使用@Component,其实等介于<bean id="" class="com.marker.object.User">
@Component
@Scope("prototype")
public class Person {
  	@Value("marker")//属性赋值
    private String name;
    private int age;
  //get、set、构造方法、toString... 自己完成
}
```

> @Service,@Component,@Controller,@Repository都是组件的意思，功能都是一样的，都代表将某个类注册到Spring种的，装配Bean

## **小结**：

* XML更加万能，适用于任何场景，方便维护
* 注解：只能在自己的类使用，不是自己的类使用不了，维护更加复杂
* XML和注解一起使用：一般XML用来管理bean，注解只负责完成属性的注入
* 使用注解之前，需要注意的是必须让注解生效，开启注解支持

## 4、注入组件

容器中注入组件的方式：@Bean导入第三方的类或包的组件、ComponentScan包扫描+组件的标注注解、@Import、使用Spring提供的FactoryBean(工厂Bean)进行注册

# 二、Spring Boot注解大全

```java
@Service: 注解在类上，表示这是一个业务层bean
@Controller：注解在类上，表示这是一个控制层bean
@Repository: 注解在类上，表示这是一个数据访问层bean
@Component： 注解在类上，表示通用bean ，value不写默认就是类名首字母小写
@Autowired：按类型注入
```

## @Configuration

```java
@Configuration：注解在类上，表示这是一个IOC容器，相当于spring的配置文件，java配置的方式。 IOC容器的配置类一般与 @Bean 注解配合使用，用 @Configuration 注解类等价与 XML 中配置 beans，用@Bean 注解方法等价于 XML 中配置 bean。
@Bean： 注解在方法上，声明当前方法返回一个Bean
```

## @Scope

```reStructuredText
@Scope：注解在类上，描述spring容器如何创建Bean实例。

（1）singleton： 表示在spring容器中的单例，通过spring容器获得该bean时总是返回唯一的实例

（2）prototype：表示每次获得bean都会生成一个新的对象

（3）request：表示在一次http请求内有效（只适用于web应用）

（4）session：表示在一个用户会话内有效（只适用于web应用）

（5）globalSession：表示在全局会话内有效（只适用于web应用）

```

## @ComponentScan

```reStructuredText
@ComponentScan：注解在类上，扫描标注了@Controller等注解的类，注册为bean 。
@ComponentScan 为 @Configuration注解的类配置组件扫描指令。@ComponentScan 注解会自动扫描指定包下的全部标有 @Component注解的类，并注册成bean，当然包括 @Component下的子注解@Service、@Repository、@Controller。
```

## @Responsebody

@Responsebody 注解表示该方法的返回的结果直接写入 HTTP 响应正文（ResponseBody）中，一般在异步获取数据时使用，通常是在使用 @RequestMapping 后，返回值通常解析为跳转路径，加上@Responsebody 后返回结果不会被解析为跳转路径，而是直接写入HTTP 响应正文中。

## @RequestMapping

```reStructuredText
1. value，指定请求的地址 
2. method 请求方法类型 这个不写的话，自适应：get或者post
3. consumes 请求的提交内容类型 
4. produces 指定返回的内容类型 仅当request请求头中的(Accept)类型中包含该指定类型才返回
5. params 指定request中必须包含某些参数值 
6. headers 指定request中必须包含指定的header值
7. name指定映射的名称  
```

>也可以使用@GetMapping、 @PostMapping、 @PutMapping、@DeleteMapping 这与上面的RequestMapping是一样的效果

# JPA注解

## @Entity@Table(name="")

@Entity：@Table(name=“”)：注解在类上表明这是一个实体类。一般用于jpa这两个注解一般一块使用，但是如果表名和实体类名相同的话，@Table可以省略。

```java
@Entity
@Table(name = "t_blog")
public class Blog {
    @Id
    @GeneratedValue
    private Long id;
}
```

## @Column

```reStructuredText
@Column：通过@Column注解设置，包含的设置如下 
name：数据库表字段名 
unique：是否唯一 
nullable：是否可以为空 
```

## @Id

@Id：表示该属性为主键。

## @GeneratedValue

```reStructuredText
@GeneratedValue(strategy = GenerationType.SEQUENCE,generator = “repair_seq”)：表示主键生成策略是sequence（可以为Auto、IDENTITY、native等，Auto表示可在多个数据库间切换），指定sequence的名字是repair_seq。
```

## @Basic(fetch=FetchType.LAZY)

@Basic(fetch=FetchType.LAZY)：标记可以指定实体属性的加载方式

## @Transient

@Transient：表示该属性并非一个到数据库表的字段的映射,ORM框架将忽略该属性。如果一个属性并非数据库表的字段映射,就务必将其标示为@Transient,否则,ORM框架默认其注解为@Basic。

## @OneToOne、@OneToMany、@ManyToOne

@OneToOne、@OneToMany、@ManyToOne：对应[hibernate](http://lib.csdn.net/base/javaee)配置文件中的一对一，一对多，多对一。

## @MappedSuperClass

@MappedSuperClass:用在确定是父类的entity上。父类的属性子类可以继承。

## @NoRepositoryBean

@NoRepositoryBean:一般用作父类的repository，有这个注解，spring不会去实例化该repository。

# 三、springMVC相关注解

## @RequestMapping

RequestMapping是一个用来处理请求地址映射的注解，可用于类或方法上。

用于类上，表示类中的所有响应请求的方法都是以该地址作为父路径。

该注解有六个属性：

* method：指定请求的method类型， GET、POST、PUT、DELETE等
* value：指定请求的实际地址，指定的地址可以是URI Template 模式

* params：指定request中必须包含某些参数值是，才让该方法处理。
* headers：指定request中必须包含某些指定的header值，才能让该方法处理请求。
* consumes：指定处理请求的提交内容类型（Content-Type），如application/json,text/html。
* produces：指定返回的内容类型，仅当request请求头中的(Accept)类型中包含该指定类型才返回。

## @RequestParam

@RequestParam：用在方法的参数前面。

## @PathVariable:路径变量

