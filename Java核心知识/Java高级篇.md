> 本文使用Java 8+

## 前言

## 第一章	集合与数组概念

以往想要存储多个对象，我们想到用StringBuffered、数组来存储多个对象。但是在写程序的时候我们并不知道将需要多少个对象，或者是否需要更复杂的方式来存储对象，结果发展数组尺寸是固定的，这也太过于受限了吧，一点都不灵活，太难了，数组能开放点吗？

随后Java团队为了解决数组长度不可变，就提供了这么一个集合(Collection)，其中基本的类型有 **List** 、 **Set** 、 **Queue** 和 **Map**。用Java集合类都可以自动地调整自己的大小。简直就要飞天了吧。因此，与数组不同是，在编程时，可以将任意数量的对象放置在集合中，而不用关心集合应该有多大。

**小结：数组和集合区别**

>**长度区别**：数组长度固定；集合的长度可变（灵活易扩展）
>
>**存储内容区别**：数组存储的是同一种类型的元素；集合可以存储不同类型的元素（但我们开发者考虑安全问题一般使用范型）
>
>**存储数据的类型区别**：数组可以存储基本数据类型,也可以存储引用类型；集合只能存储引用类型(如存储int，它会自动装箱成Integer)

## 第二章	集合框架

### 1、集合的两大类

**集合按照其存储结构可以分为两大类，分别是**：

* 单列集合`java.util.Collection`，collection是单列集合类的根接口，它还有两个子接口分别是`List`和`set`接口。
  * `List`接口的特点：元素有序、元素可重复。（主要实现类有ArrayList、LinkedList、vector）
  * `Set`接口的特点：元素无序，且不可重复。（主要实现类有HashSet、TreeSet、LinkedHashSet）

* 双列集合`java.util.Map`

> 集合本身是一个工具，它存放在java.util包中。

### 2、集合创建

```java
List<Apple> apples = new ArrayList<>();
List<String> s = new ArrayList<>();
```

**注意**： **ArrayList** 已经被向上转型为了 **List** ，使用接口的目的是，如果想要改变具体实现，只需在创建时修改它就行了，就像下面这样：

```java
List<Apple> apples = new LinkedList<>();
```

>注意：向上转型并非总是有效，因为某些具体类有额外的功能，就比如 **LinkedList** 具有 **List** 接口中未包含的额外方法，因此，就不能将它们向上转型为更通用的接口。

### 3、集合为何需要泛型

使用 Java 5 之前的集合的一个主要问题是编译器允许你向集合中插入不正确的类型，早期的Java使用Object来代表任意类型的，但是向下转型有强转的问题，这样就不太好了，程序不太安全。

Java泛型设计原则：在编译期防止将错误类型的对象放置到集合中，运行时期就不会出现ClassCastException异常。

> 细节：在 Java 7 之前，必须要在两端都进行类型声明
>
> ArrayList<Apple> apples = new ArrayList<Apple>();

**泛型小结**：有了泛型，代码就会更加简洁**不用强制转换**、程序更加健壮、可读性和稳定性。

## 第三章 List接口

 **List** 接口在 **Collection** 的基础上添加了许多方法，允许在 **List** 的中间插入和删除元素。

### 1、List接口主要实现类

* ArrayList：擅长随机访问元素，但在 **List** 中间插入和删除元素时速度较慢。
* LinkedList：随机访问来说相对较慢，但它具有比 **ArrayList** 更大的特征集，它通过代价较低的在 **List** 中间进行的插入和删除操作，提供了优化的顺序访问。
* vector，老版本，这里就不多说了，现在都到Java14

### 2、迭代器

#### 2.1 为什么要迭代器

我们都知道集合无非就是`存储`和``获取``的这样一个过程。毕竟，保存事物是集合最基本的工作，就比如List，插入是`add()`获取是`get()`,那么会发现要使用集合，必须对集合的确切类型编程。突然有一天，如果原本是对 **List** 编码的，但是后来发现如果能够将相同的代码应用于 **Set** 会更方便，此时应该怎么做？想到可能编写一段通用代码，但是它是用于不同类型的集合，那么如何才能不重写代码就可以应用于不同类型的集合？这时就使用*迭代器*。

#### 2.2 什么是迭代器

**迭代器**（也是一种设计模式）的概念实现了这种抽象。**迭代器**是一个对象，它的工作就是遍历并选择序列中的每个对象，也就是提供了一种访问容器对象中的各个元素，并遍历出来。程序员不需要关心容器底层结构，就可以完美对容器进行遍历。

#### 2.3 使用迭代器注意事项

（1）使用 `iterator()` 方法要求集合返回一个 **Iterator**。 **Iterator** 将准备好返回序列中的第一个元素。

（2）使用 `next()` 方法获得序列中的下一个元素。

（3）使用 `hasNext()` 方法检查序列中是否还有元素。

（4）使用 `remove()` 方法将迭代器最近返回的那个元素删除。

#### 2.4 迭代器遍历的三种方法

````java
List<String> list = new ArrayList<>();
        list.add("1、kobe");
        list.add("2、詹姆斯");
        list.add("3、字母哥");
        list.add("4、浓眉");
        list.add("5、乔丹");
````

**第一种：for循环遍历（推荐）**

````java
for (Iterator it = list.iterator();it.hasNext();) {
            System.out.println(it.next());
        }
````

**第二种：声明周期就在大括号内（高并发推荐）**

```java
List<String> list = new ArrayList<>();
        list.add("1、kobe");
        list.add("2、詹姆斯");
				{
          Iterator<String> iterator = list.iterator();
          while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
       }
```

**第三种：通过迭代器遍历**

```java
Iterator<String> iterator = list.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
```

### 3、ListIterator是什么

> ListIterator是继承自Iterator

**ListIterator** 是一个更强大的 **Iterator** 子类型，它只能由各种 **List** 类生成。 **Iterator** 只能向前移动，而 **ListIterator** 可以双向移动。

**ListIterator迭代器包含的方法有**：

* `add(E e): 将指定的元素插入列表，插入位置为迭代器当前位置之前`
* `hasNext()：以正向遍历列表`
* `hasPrevious():如果以逆向遍历列表，列表迭代器前面还有元素，则返回 true，否则返回false`
* `next()：返回列表中ListIterator指向位置后面的元素`
* `nextIndex():返回列表中ListIterator所需位置后面元素的索引`
* `set(E e)：从列表中将next()或previous()返回的最后一个元素返回的最后一个元素更改为指定元素e`
* `previous():返回列表中ListIterator指向位置前面的元素`
* `previousIndex()：返回列表中ListIterator所需位置前面元素的索引`

代码演示：

```java
List<String> list = new ArrayList<>();
        list.add("kobe");
        list.add("詹姆斯");
        list.add("字母哥");
        list.add("浓眉");
        list.add("乔丹");
        ListIterator<String> listIterator = list.listIterator();
        while (listIterator.hasNext()) {
            System.out.println(listIterator.next() + "," + listIterator.nextIndex());
        }
最终输出：
kobe, 1
詹姆斯,2
字母哥,3
浓眉, 4
乔丹, 5
```

### 4、List接口常见面试题

> 说一下List、Set、Map三者的区别？

```reStructuredText
* List是有序可重复
* Set是无序不可重复
* Map它是采用键值对来存储，两个Key可以引用相同的对象，但Key不能重复，典型的Key是String类型，但也可以是任何对象。
```



> Arraylist 与 LinkedList 区别?

```reStructuredText
1. Arraylist底层使用的是数组；LinkedList底层使用的是双向链表,数据结构（JDK1.6之前为循环链表，JDK1.7取消了循环。（面试官有可能让你聊聊什么单链表和双链表）
2. Arraylist擅长随机访问元素，快速随机访问就是通过元素的序号快速获取元素对象(对应于get(int index)方法)。LinkedList`随机访问来说相对较慢，但它具有比ArrayList更大的特征集。
3. ArrayList采用数组存储，所以插入和删除元素的时间复杂度受元素位置的影响。比如：执行add(E e) 方法的时候， ArrayList 会默认在将指定的元素追加到此列表的末尾，这种情况时间复杂度就是O(1)。但是如果要在指定位置 i 插入和删除元素的话（add(int index, E element)）时间复杂度就为 O(n-i)。因为在进行上述操作的时候集合中第 i 和第 i 个元素之后的(n-i)个元素都要执行向后位/向前移一位的操作。 
4. LinkedList采用链表存储，所以对于`add(E e)`方法的插入，删除元素时间复杂度不受元素位置的影响，近似 O(1)，如果是要在指定位置`i`插入和删除元素的话（(add(int index, E element)`） 时间复杂度近似为`o(n))`因为需要先移动到指定位置再插入。
```



> 补充单链表、双链表、双向循环链表

```reStructuredText
单链表：一个节点指向下一个节点（操作链表要时刻记住的是：节点中指针域指向的就是一个节点了！）

双向链表：包含两个指针，一个prev指向前一个节点，一个next指向后一个节点。

双向循环链表：最后一个节点的 next 指向head，而 head 的prev指向最后一个节点，构成一个环。
```

推荐阅读：[看图轻松理解数据结构与算法系列(双向链表)](https://juejin.im/post/5b5d1a9af265da0f47352f14)

> 说一下ArrayList 与 Vector 区别呢?为什么要用Arraylist取代Vector呢？

```reStructuredText
Vector类的所有方法都是同步的。可以由两个线程安全地访问一个Vector对象、但是一个线程访问Vector的话代码要在同步操作上耗费大量的时间。

Arraylist不是同步的，所以在不需要保证线程安全时建议使用Arraylist。
```



> ArrayList的大小是如何自动增加的？

```reStructuredText
1、添加元素时，首先进行判断是否大于默认容量10
2、如果，小于默认容量，直接在原来基础上+1，元素添加完毕
3、如果，大于默认容量，则需要进行扩容，扩容核心是grow()方法
   3.1 扩容之前，首先创建一个新的数组，且旧数组被复制到新的数组中
       这样就得到了一个全新的副本，我们在操作时就不会影响原来数组了
   3.2 然后通过位运算符将新的容量更新为旧容量的1.5陪（原来长度的一半再加上原长度也就是每次扩容是原来的1.5倍）
   3.3 如果新的容量-旧的容量<=0，就拿新的容量-最大容量长度如果<=0的，那么最终容量就是扩容后的容量
```

> 什么情况下你会使用ArrayList？什么时候你会选择LinkedList？

```reStructuredText
1. 当你遇到访问元素比插入或者是删除元素更加频繁的时候，你应该使用ArrayList
2. 插入或者是删除元素更加频繁，或者你压根就不需要访问元素的时候，你会选择LinkedList
```

> 由上面问题，引入下一个问题：ArrayList插入和删除效率为什么这么低？

```reStructuredText
因为在ArrayList中增加或者删除某个元素，通常会调用System.arraycopy方法，这是一种极为消耗资源的操作，因此，在频繁的插入或者是删除元素的情况下，LinkedList的性能会更加好一点。
```



## 第四章 链表LinkedList

**LinkedList** 和 **ArrayList** 一样实现了基本的 **List** 接口，但它在 **List** 中间执行插入和删除操作时比 **ArrayList** 更高效。而在随机访问操作效率方面却要逊色一些。链表擅长增删。

> LinkedList 还添加了一些方法，使其可以被用作栈、队列或双端队列（deque）

1. 链表是以节点的方式来存储，链式存储。

2. 每个节点包含data域，next域：指向下一个节点。
3. 链表存储不一定是连续的。

添加操作：

1. 先创建一个head头节点，作用就是表示链表的头
2. 后面每添加一个节点，就直接加入到链表的最后

遍历：

1. 通过一个辅助变量遍历，帮组遍历整个链表





## 第五章 set接口

**Set** 不保存重复的元素，**Set** 最常见的用途是测试归属性，可以很轻松地询问某个对象是否在一个 **Set** 中

