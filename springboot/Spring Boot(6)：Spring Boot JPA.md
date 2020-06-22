## 前言

Spring Data JPA为Java Persistence API（JPA）提供了存储库支持，它简化了需要访问JPA数据源的应用程序的开发。是Sun公司提出的 Java 持久化规范，为 Java 开发人员提供了一种**将对象“映射”到关系数据库**，主要是为了简化现有的持久化开发工作和整合 ORM 技术的。



## 如何使用Spring Data JPA

### 1、pom文件添加依赖

```xml
<dependency>
    <groupId>org.springframework.data</groupId>
    <artifactId>spring-data-jpa</artifactId>
</dependency>
```

### 2、本文使用mysql作为讲解，因此在pom文件添加数据库驱动

```xml
<dependency>
<groupid>mysql</groupid> 
<artifactid>mysql-connector-java</artifactid>
</dependency>
```

### 3、基本的查询

其实Spring Boot Jpa 默认预先生成了一些基本的CURD的方法（增、删、改）等等了，只需要编写一个抽象类去继承即可，首先在创建`dao`层，该层与controller都是同级的。

**编写抽象类UserRepository如下**：

```java
package com.markjava.dao;

import com.markjava.po.User;
import org.springframework.data.jpa.repository.JpaRepository;
public interface UserRepository extends JpaRepository<User, Long> {
    
}
```

**编写实体类User类如下**：

```java
package com.markjava.po;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;
import javax.persistence.Table;

@Entity
@Table(name = "t_user")
public class User {
    @Id
    @GeneratedValue
    private Integer id;
    private String name;
    private String age;

}
```

注：如果在实体User写`@Entity and @Table(name = "t_user")`时，没有提示或者不显示出来，请手动导入这几个包包。

```xml
import javax.persistence.Entity; 
import javax.persistence.GeneratedValue;
import javax.persistence.Id;
```

