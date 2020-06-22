## 前言

> 本文是笔者复习Java8版本的时候，记录下来的知识点。本文只复习Java基础部分，而Java高级部分在另一篇来总结。

@[TOC](目录)

## 第一章：运算符

### 1、Java基本类型

| 基本类型 |  大小   |  最小值   |     最大值     | 包装类型  |
| :------: | :-----: | :-------: | :------------: | :-------: |
| boolean  |    —    |     —     |       —        |  Boolean  |
|   char   | 16 bits | Unicode 0 | Unicode 216 -1 | Character |
|   byte   | 8 bits  |   -128    |      +127      |   Byte    |
|  short   | 16 bits |   - 215   |    + 215 -1    |   Short   |
|   int    | 32 bits |   - 231   |    + 231 -1    |  Integer  |
|   long   | 64 bits |   - 263   |    + 263 -1    |   Long    |
|  float   | 32 bits |  IEEE754  |    IEEE754     |   Float   |
|  double  | 64 bits |  IEEE754  |    IEEE754     |  Double   |
|   void   |    —    |     —     |       —        |   Void    |

### 2、自增自减运算

* `++`运算变量自己增长1。反之， `--`运算，变量自己减少1

变量在独立运算时， 前++ 和后++ 没有区别 (++a<=>a=a+1)。

* 混合运算,变量前++ :变量a自己加1，将加1后的结果赋值给b，也就是说a先计算。a和b的结果都是2。

### 3、赋值运算符

````java
public static void main(String[] args){ 
	int i = 5;
	i+=5;
	//计算方式 i=i+5 变量i先加5，再赋值变量i,累加
	System.out.println(i); //输出结果是10 
}
````

### 4、逻辑运算符

与`&&`、或`||`取反`!`

### 5、三元运算符

格式:`数据类型 变量名 = 布尔类型表达式?结果1:结果2`

计算方式:`布尔类型表达式结果是true，三元运算符整体结果为结果1，赋值给变量`、`布尔类型表达式结果是false，三元运算符整体结果为结果2，赋值给变量`

三元运算符用于做判断，其等价的if-else语句如下： 

````java
//获取三个整数中的最大值
int a = 10;
int b = 20;
int c = 30;
//先比较任意两个数的值，找出这两个数中的最大值
int temp = (a > b) ? a : b;
//用前两个数的最大值与第三个数比较，获取最大值
int max = (temp > c) ? temp : c;
System.out.println("max = " + max);
````

总结：

**布尔表达式 ? 值 1 : 值 2**

若表达式计算为 **true**，则返回结果 **值 1** ；如果表达式的计算为 **false**，则返回结果 **值 2**。

### 6、移位运算符

移位运算符面向的运算对象也是二进制的“位”。它们只能用于处理整数类型（基本类型的一种）

* 左移位运算符`<<`能将其左边的运算对象向左移动右侧指定的位数（在低位补 0）

* 右移位运算符 `>>`右移位运算符有“正”、“负”值：若值为正，则在高位插入 0；若值为负，则在高位插入 1。

## 第二章：控制流

在 Java 中，控制流涉及的关键字包括 **if-else，while，do-while，for，return，break** 和选择语句 **switch**。

### 1、if-else

**if-else** 语句是控制程序执行流程最基本的形式。其中 `else` 是可选的，因此可以有两种形式的 `if`。代码示例：

````java
//不用else
int a = 2;
int b = 1;
int result = 0;
if (a > b) {
		result = +1;
}
//用else
if （） {
  "hello"
}else {"no hello"}
````

### 2、迭代语句while

**while**，**do-while** 和 **for** 用来控制循环语句（有时也称迭代语句）。只有控制循环的布尔表达式计算结果为 `false`，循环语句才会停止。

**例子：while循环计算1-100之间的和**

````java
public class Demo {
    public static void main(String[] args) {
        int i = 1;
        int sum = 0;
        while (i <= 5) {
            sum += i;
            i++;
        }
        System.out.println(sum);
    }
}
//结果sum=15
````

### 3、for循环

**for** 循环可能是最常用的迭代形式。 该循环在第一次迭代之前执行初始化。随后，它会执行布尔表达式，并在每次迭代结束时，进行某种形式的步进。

**例子：使用循环，计算1-100之间的偶数和**

````java
public class Demo {
    public static void main(String[] args) {
        int sum = 0;
        for (int i = 1; i <=5; i++) {
            if (i % 2 == 0) {
                sum += i;
            }
            System.out.println("偶数之和：" + sum);
        }
    }
}
````

### 4、嵌套循环

**嵌套循环格式**

````
 for(初始化表达式1; 循环条件2; 步进表达式7) { 
 for(初始化表达式3; 循环条件4; 步进表达式6) {
			执行语句5; 
	}
}
````

**嵌套循环执行流程**

```
1. 执行顺序:123456>456>723456>456 
2. 外循环一次，内循环多次。 
3. 比如跳绳:一共跳5组，每组跳10个。5组就是外循环，10个就是内循环。
```

**例子：5x8的矩形，打印5行x号，每行8个**

```java
public class Demo {
    public static void main(String[] args) {
        for (int i = 0; i < 5; i++) {
            for (int j = 0; j < 8; j++) {
                System.out.print("*");
            }
            System.out.println();
        }
    }
}
```

## 第三章：继承

### 1、为什么要继承？

当多个类中存在相同属性和行为时候，我们将这些相同的内容抽取到单独一个类中，其他类就不需要再定义这些属性和行为，只要继承那一个类即可。

### 2、继承定义

就是子类继承父类的**属性和行为**，使得子类对象具有与父类相同的属性、相同的行为。**子类可以直接 访问父类中的非私有的属性和行为。**

### 3、继承好处

* 提高代码的复用性
* 使用继承使得类和类之间产生了关联，是多态的前提

### 4、继承后的特点

#### 4.1	继承后子类成员变量有何影响

**子类成员变量和父类不重名**：如果子类父类中出现不重名的成员变量，这时的访问是没有影响的，可以直接访问父类。

**子类成员变量和父类重名**：如果子类父类中出现重名的成员变量，这时就访问不了父类成员属性了。如果想要在子类中访问父类中非私有成员变量时，需要使用`super`关键字来修饰父类成员变量，`super.父类成员变量名`。

> 如果父类中的成员变量是非私有的，子类中可以直接访问。那么，如果父类中的成员变量私有的，子类是不能直接访问了。通常编码时，我们遵循封装的原则，使用private修饰成员变量，那么如何访问父类的私有成员变量呢?这时，可以在父类中提供公共的get方法和set方法即可访问。

#### 4.2	继承后子类成员方法有何影响

**子类成员方法和父类不重名**：和成员变量一样，调用没有任何影响的。不过调用方法时，会先在子类中查找有没有对应的方法，若子类中存在就会执行子类中的方法，若子类中不存在就会执行父类中相应的方法。

**子类成员方法和父类重名（重写Override）**：**重写(Override)**是指子类父类中出现重名的成员方法，称为方法重写 (Override)。

**方法重写 **：如果子类中出现与父类一模一样的方法时及(返回值类型，方法名和参数列表都相同)，它将会覆盖父类效果，称为**重写**，声明不变，重新实现。

#### 4.3	重写有什么用？

子类可以根据需要，定义自己的行为。

#### 4.4	子类为什么不可以继承父类构造方法

* 我们知道构造方法的名字是与类名一致的，所以子类是无法继承父类构造方法的。

* 构造方法的作用是初始化成员变量的，当子类的初始化过程中，必须先执行父类的初始化动作。

> 其实子类的构造方法中默认有一个 super() ，表示调用父类的构造方法，父类成员变量初始化后，才可以给子类使用。

#### 4.5	重载与重写的区别

**重载（overloaded）**： 是指在同一个类中允许同时存在2个以上同名方法，但方法的参数个数或类型不同。**重载的规则：**必须具有不同的参数列表；返回类型可以不同，只需参数类表不同即可；访问修饰符可以不同；可以抛出不同异常。

**重写（override）**：有时称为**覆盖**，是指子类与父类的成员方法相同（包括返回类型、参数），然后子类可以实现自己行为。

### 5、小结

* 子类方法覆盖父类方法，必须要保证权限大于等于父类权限。

* 子类方法覆盖父类方法，返回值类型、函数名和参数列表都要一模一样。

### 6、super和this的区别

* super：代表父类的存储空间标识

* this：代表当前对象的引用

> 注：Java只支持单继承，不支持多继承，但可以实现任意多个接口。

## 第四章：封装

封装可以隐藏对数据操作的具体细节，安全性有保障，可以更方便的调用封装的部分

即便封装之后依然能够利用反射访问私有属性或者反射调用私有的方法

## 第四五：多态

### 1、概述

多态是Java面向对象的三大特性之一，多态主要是消除类型之间的一个耦合关系。多态不仅能改善代码的组织，提高代码的可读性，而且能创建有扩展性的程序——无论在最初创建项目时还是在添加新特性时都可以“生长”的程序。

### 2、多态存在三个必要的条件

* 要有继承

* 要有重写
* 父类引用指向子类对象

### 3、使用多态的好处

* 可替换性。多态对已存在的代码具有可替换性。

* 可扩展性。增加新的子类不影响已存在类的多态性，继承性，以及其他特性的运行和操作，实际上新增功能更容易获得多态功能。

* 接口性。多态是超类通过方法签名。向子类提供一个共同接口，由子类来完善或者覆盖它而实现的。

* 灵活性。他在应用中提现了灵活多样的操作，提高了使用效率。

* 简化性。多态简化对应用软件的代码编写和修改过程，尤其是在处理大量对象的运算和操作时，这个特点尤为突出和重要。

## 六、接口

### 1、接口创建(Java8之前)

使用 **interface** 关键字创建接口，在 Java 8之前我们可以这么说：**interface** 关键字产生一个完全抽象的类，没有提供任何实现。我们只能描述类应该像什么，做什么，但不能描述怎么做，即只能决定方法名、参数列表和返回类型，但是无法确定方法体。**接口只提供形式，通常来说没有实现，尽管在某些受限制的情况下可以有实现**。

> 接口被用来建立类之间的协议

### 2、接口创建(Java8中)

Java 8 中接口稍微有些变化，它为关键字 ``default`` 增加了一个新的用途(之前只用于 **switch** 语句和注解中），我们来看一个例子

````java
//定义一个接口
interface AnInterface {
    void firstMethod();
    void secondMethod();
}
//实现接口
public class AnImplementation implements AnInterface {
    public void firstMethod() {
        System.out.println("firstMethod");
    }

    public void secondMethod() {
        System.out.println("secondMethod");
    }
}
````

注：如果我们在 **AnInterface** 中增加一个新方法 `newMethod()`，而在 ``AnImplementation`` 中没有实现它，编译器就会报错的。

如果我们想在``AnInterface``中增加一个新方法 `newMethod()`，而不想让它报错，怎么办？这时使用关键字 **default** 为 `newMethod()` 方法提供默认的实现这样就不会报错啦。

````java
//实现接口
public class AnImplementation implements AnInterface {
    public void firstMethod() {
        System.out.println("firstMethod");
    }

    public void secondMethod() {
        System.out.println("secondMethod");
    }
  	//使用default
  	default void newMethod() {
        System.out.println("newMethod");
    }
}
````

## 七、内部类

> 一个定义在另一个类中的类，叫作内部类。

内部类是一种非常有用的特性，因为它允许你把一些逻辑相关的类组织在一起，并控制位于内部的类的可见性。用内部类写出的代码更加优雅而清晰，尽管并不总是这样（而且 Java 8 的 Lambda 表达式和方法引用减少了编写内部类的需求）。

### 1、创建内部类

定义格式：

```java
class 外部类 { 
	class 内部类{
	} 
}
```

```java
//外部类
public class Person {
	private boolean live = true; 
  //内部类
  class Heart {
		public void jump() {
			// 直接访问外部类成员 
      if (live) {
					System.out.println("心脏在跳动"); 
      } else {
					System.out.println("心脏不跳了"); 
      }
		} 
 }
public boolean isLive() { 
  return live;
}
public void setLive(boolean live) { 
  this.live = live;
	} 
}
//定义测试类
public class InnerDemo {
   public static void main(String[] args) { 
  	// 创建外部类对象
		Person p = new Person(); 
      // 创建内部类对象
			Heart heart = p.new Heart();
			// 调用内部类方法 
      heart.jump();
			// 调用外部类方法 
      p.setLive(false); 
      // 调用内部类方法 
      heart.jump();
} }
//输出结果: 
 心脏在跳动 
 心脏不跳了
```

### 2、什么时候使用内部类？

比如在描述事物时，若一个事物内部还包含其他事物，就可以使用内部类这种结构。

### 3、内部类访问特点

* 内部类可以直接访问外部类的成员，包括私有成员。

* 外部类要访问内部类的成员，必须要建立内部类的对象。

## 八、匿名内部类【重点】

> 匿名内部类必须继承一个父类或者实现一个父接口。匿名内部类适合创建那种只需要一次使用的类。

**格式例子**：

````java
interface Product{
   public double getPrice();
   public String getName();
}
public class AnonymousTest{
   public void test(Product p){
      System.out.println("购买了一个" + p.getName()
         + "，花掉了" + p.getPrice());
   }
  
   public static void main(String[] args){
      AnonymousTest ta = new AnonymousTest();
      // 调用test()方法时，需要传入一个Product参数，
      // 此处传入其匿名内部类的实例
      ta.test(new Product(){
         public double getPrice(){
            return 567.8;
         }
         public String getName(){
            return "AGP显卡";
         }
      });
   }
}
````

本篇文章如有错的地方，欢迎在评论指正。喜欢在微信看技术文章，可以微信搜索「MarkerJava」，回复【Java】【大数据】【Spring全家桶】【电子书籍】即可获得精品全套视频，还有更多资料，建议后台留言或者直接私信我。

> 另，如果觉得这本篇文章写得不错，有点东西的话，各位人才记得来个三连【点赞+关注+分享】。