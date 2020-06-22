## 前言

**本文详细解读ArrayList、LinkedList及Vector源码，包括他们实现方法的底层源码，一步一步教你读懂源码。**

> 声明，本文用的是jdk1.8+

> 注：源码解析部分比较细，建议放慢速度一步一步去理解

@[TOC](目录)

### 一、集合和数组区别

#### 1. 集合是什么

比如想要存储多个对象，能想到的是一个**容器**，没有学过集合之前可能用到的是StringBuffered、数组。但是问题来了，数组的长度是不可变的，扩展不灵活，那该怎么办？随后Java团队为了解决数组长度不可变，就提供了这么一个集合(Collection)。

#### 2. 与数组的区别

**长度区别**：数组长度固定；集合的长度可变（灵活易扩展）

**存储内容区别**：数组存储的是同一种类型的元素；集合可以存储不同类型的元素（但我们开发者考虑安全问题一般使用范型）

**存储数据的类型区别**：数组可以存储基本数据类型,也可以存储引用类型；集合只能存储引用类型(如存储int，它会自动装箱成Integer)

### 二、List集合介绍

#### **1. List特点**：有序(存储顺序和取出顺序一致)，可重复

#### **2. List常用的3个子类**

- **ArrayList**
  * 底层数据结构是数组（线程不安全）

- **LinkedList**
  * 底层数据结构是链表（线程不安全）

- **Vector**
  * 底层数据结构是数组（线程安全）


### 三、ArrayList、LinkedList、Vector源码解析

> 好了，不BB太多，直接上源码

#### 1.  ArrayList

````java
常用方法：
  add()
  get()
  set()
  remove()
````

##### 1.1 先了解ArrayList的几个属性

````java
//ArrayList初始容量为10
private static final int DEFAULT_CAPACITY = 10;
//用于空实例的共享空数组实例
private static final Object[] EMPTY_ELEMENTDATA = {};
//用于默认大小空实例的共享空数组实例。
//我们把它从EMPTY_ELEMENTDATA数组中区分出来，以知道在添加第一个元素时容量需要增加多少
private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
//保存ArrayList数据的数组
transient Object[] elementData; 
//该属性设置容量大小，ArrayList 所包含的元素个数
private int size;
````

验证上面讲的几个属性

````java
public ArrayList(int initialCapacity) {
  			//如果指定容量大于0，那么数组就进行初始化成对应的容量
        if (initialCapacity > 0) {
            this.elementData = new Object[initialCapacity];
        } else if (initialCapacity == 0) {
          //如果初始容量为0，则默认返回空的数组EMPTY_ELEMENTDATA
            this.elementData = EMPTY_ELEMENTDATA;
        } else {
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        }
    }
//默认返回的DEFAULTCAPACITY_EMPTY_ELEMENTDATA空数组，
//也就是说初始其实是空数组 当添加第一个元素的时候数组容量才变成10
public ArrayList() {
        this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
    }
````

##### 1.2 add方法（重点）

具体实现步骤分为：

* 首先判断是否需要扩容
* 再插入元素

##### 1.3 当容量长度小于默认容量的长度10时，源码解析（5❤️）

````java
//1、添加元素
public boolean add(E e) {
        modCount++;
        add(e, elementData, size);
        return true;
    }
//2、判断是否需要扩容
private void add(E e, Object[] elementData, int s) {
  			//判断s长度是否等于默认容量长度10
  			//如果大于默认容量10，则走扩容elementData = grow();
  			//如果小于默认容量10，则走直接添加elementData[s] = e;
        if (s == elementData.length)
            elementData = grow();
        elementData[s] = e;
  			//小于默认容量长度10，则size等于原来的+1
        size = s + 1;
    }

//3、最终返回size = s + 1;
public boolean add(E e) {
        modCount++;
        add(e, elementData, size);
  			//最终的返回
        return true;
    }

````

##### 1.4 当容量长度大于默认容量的长度10时，源码解析（5❤️）

````java
//1、添加元素
public boolean add(E e) {
        modCount++;
        add(e, elementData, size);
        return true;
    }
//2、判断是否需要扩容
private void add(E e, Object[] elementData, int s) {
  			//判断s长度是否等于默认容量长度10
  			//如果大于默认容量10，则走扩容elementData = grow();
  			//如果小于默认容量10，则走直接添加elementData[s] = e;
        if (s == elementData.length)
            elementData = grow();
        elementData[s] = e;
        size = s + 1;
    }
//3、默认容量+1
private Object[] grow() {
        return grow(size + 1);
    }
//4、minCapacity=默认容量size+1
private Object[] grow(int minCapacity) {
        return elementData = Arrays.copyOf(elementData,
                                         newCapacity(minCapacity));//进行扩容了
    }
//5、实现扩容
//用来确定是否需要容量，最后调用grow()来扩容
//假设当添加11个元素时，minCapacity=11，而默认数组为10，此时需要进行扩容
private int newCapacity(int minCapacity) {
        // overflow-conscious code
  			//5.1 旧容量10
  			//扩容1.5倍
        int oldCapacity = elementData.length;
  			//5.2新的容量=旧容量+(oldCapacity >> 1)
        int newCapacity = oldCapacity + (oldCapacity >> 1);
  			//5.3判断，如果新的容量-最小容量<=0(也就是扩容后的容量-minCapacity)
  			//minCapacity(最小容量=默认的容量10+你存入元素得到的长度)
        if (newCapacity - minCapacity <= 0) {
            if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA)
                return Math.max(DEFAULT_CAPACITY, minCapacity);
            if (minCapacity < 0) // overflow
                throw new OutOfMemoryError();
            return minCapacity;
        }
  			//5.4新的容量减去MAX_ARRAY_SIZE <= 0的话，就等于扩容后的长度
        return (newCapacity - MAX_ARRAY_SIZE <= 0)
            ? newCapacity
            : hugeCapacity(minCapacity);
    }
//6、得到最小容量（就是假如你存入长度为11，则最终minCapacity=11的意思）
private Object[] grow(int minCapacity) {
        return elementData = Arrays.copyOf(elementData,
                                           newCapacity(minCapacity));
    }
		//7、newLength扩容后的长度、original原始的
    @SuppressWarnings("unchecked")
    public static <T> T[] copyOf(T[] original, int newLength) {
        return (T[]) copyOf(original, newLength, original.getClass());
    }
		//8、数组的拷贝复制
    public static <T,U> T[] copyOf(U[] original, int newLength, Class<? extends T[]> newType) {
        @SuppressWarnings("unchecked")
        T[] copy = ((Object)newType == (Object)Object[].class)
            ? (T[]) new Object[newLength]
            : (T[]) Array.newInstance(newType.getComponentType(), newLength);
      	//Math.min进行计算
        System.arraycopy(original, 0, copy, 0,
                         Math.min(original.length, newLength));
        return copy;
    }
		//9、a=默认容量10，b=扩容后的容量
    public static int min(int a, int b) {
        return (a <= b) ? a : b;
    }
//10、再回到System.arraycopy(original, 0, copy, 0,
public static <T,U> T[] copyOf(U[] original, int newLength, Class<? extends T[]> newType) {
        @SuppressWarnings("unchecked")
        T[] copy = ((Object)newType == (Object)Object[].class)
            ? (T[]) new Object[newLength]
            : (T[]) Array.newInstance(newType.getComponentType(), newLength);
  			//复制
        System.arraycopy(original, 0, copy, 0,
                         Math.min(original.length, newLength));
  			//最终返回复制所得到的
        return copy;
    }
//11、复制结束return (T[]) copyOf(original, newLength,original.getClass());
public static <T> T[] copyOf(T[] original, int newLength) {
        return (T[]) copyOf(original, newLength, original.getClass());
    }
//12、grow()方法扩容得到最终的长度
private Object[] grow() {
        return grow(size + 1);
    }
//13、
private void add(E e, Object[] elementData, int s) {
        if (s == elementData.length)
          	//13.1	elementData = grow();
            elementData = grow();
  			//13.2	e是存入的元素
        elementData[s] = e;
  			//13.3最终长度为size = s + 1;
        size = s + 1;
    }
//14、结束
public boolean add(E e) {
        modCount++;
        add(e, elementData, size);
  			//返回
        return true;
    }
````

**总结**：添加（add）方法的实现，添加时首先检查数组的容量是否满足

* ArrayList首次扩容，容量为10，不满足则扩容到到原来的1.5倍
* 第一次扩容后，容量还是小于minCapacity（就是我们添加的容量如15，则minCapacity=15），就将容量扩容为minCapacity
* 如果容量足够就直接添加，不够就需要进行扩容
* 扩容过程的空间复杂度是O(2n)，因为需要拷贝一个次旧的数组
* ArrayList是**基于动态数组实现的**，在增删时候，需要数组的拷贝复制

##### 1.5 get、set、remove方法

````java
//get方法
public E get(int index) {
  			//检查角标
        Objects.checkIndex(index, size);
        return elementData(index);//返回
    }
//set方法
public E set(int index, E element) {
        Objects.checkIndex(index, size);
  			//将进行替换，返回旧值
        E oldValue = elementData(index);
        elementData[index] = element;
        return oldValue;
    }
//remove方法实现步骤：
//1.检查角标
//2.删除元素
//3.计算出需要移动个数
//设置null，GC回收
//每次删除一个数，后边的元素会往前移动
public E remove(int index) {
        Objects.checkIndex(index, size);
        final Object[] es = elementData;

        @SuppressWarnings("unchecked") E oldValue = (E) es[index];
        fastRemove(es, index);

        return oldValue;
    }
````

**使用remove的应用Iterator遍历删除是最安全的**如下：

````java
public void testRemove(){
    List<String> strings = new ArrayList<>();
    strings.add("1");
    strings.add("2");
    strings.add("3");
    strings.add("4");
    Iterator<String> iterator = strings.iterator();
    while (iterator.hasNext()){
        String next = iterator.next();
        iterator.remove();
    }
 
    System.out.println(strings);
}
````

ArrayList总结：

* ArrayList的默认初始化容量是10，每次扩容时候增加原先容量的一半，也就是变为原来的1.5倍
* 删除元素时不会减少容量大小，需要减少容量，就调用**trimToSize()**
* 底层数组结构，线程不安全
* 动态数组实现，增删时候，需要数组的拷贝复制
* 第一次扩容后，如果容量还是小于minCapacity，就将容量扩充为minCapacity。

#### 2、LinkedList

> LinkedList比较简单易理解多了，接下来有些地方可能不会过多解释

要知道LinkedList底层实现是【双向链表】的，接下来上源码即可，首先看两个构造方法吧。

````java
public LinkedList() {
    }
public LinkedList(Collection<? extends E> c) {
        this();
        addAll(c);
    }
````

其实只要你把前面ArrayList的方法学好，基本也没有什么问题了，接下来我看几个常用的方法即可。

##### 2.1 添加(add)、get、remove方法

**add方法**

````java
//add方法
//大家猜想一下，其实该方法就是把在链表最后添加元素的
public void add(int index, E element) {
        checkPositionIndex(index);
        if (index == size)
            linkLast(element);
        else
            linkBefore(element, node(index));
    }
//linkLast(element)实现的方法
void linkLast(E e) {
        final Node<E> l = last;
        final Node<E> newNode = new Node<>(l, e, null);
        last = newNode;
        if (l == null)
            first = newNode;
        else
            l.next = newNode;
        size++;
        modCount++;
    }
````

**get方法**

`````java
public E get(int index) {
        checkElementIndex(index);
        return node(index).item;
    }
//node(index).item
Node<E> node(int index) {
        // assert isElementIndex(index);
  			//下标长度的一半，从头遍历
        if (index < (size >> 1)) {
            Node<E> x = first;
            for (int i = 0; i < index; i++)
                x = x.next;
            return x;
        } else {//否则从尾部遍历
            Node<E> x = last;
            for (int i = size - 1; i > index; i--)
                x = x.prev;
            return x;
        }
    }
`````

**remove方法**

```java
public boolean remove(Object o) {
        if (o == null) {
            for (Node<E> x = first; x != null; x = x.next) {
                if (x.item == null) {
                    unlink(x);//删除
                    return true;
                }
            }
        } else {
            for (Node<E> x = first; x != null; x = x.next) {
              	//使用equals判断的
                if (o.equals(x.item)) {
                    unlink(x);
                    return true;
                }
            }
        }
        return false;
    }
```

**注**：LinkedList方法很多，基本都是可以通过源码慢慢看，我就不一一罗列了，有兴趣的可以自己通过一行一行看，其实源码都差不多的。

#### 3、Vector

关于Vector主要看以下几点即可

* 底层数组实现，现在基本很少用，开发基本用ArrayList来替代
* 线程安全，原因底层所有方法都是同步的，所以在性能方面有损失，则效率低

#### 4、集合遍历方式

````java
import java.util.*;
public class Demo {
        public static void main(String[] args) {
            List<String> list = new ArrayList();
            list.add("kobe");
            list.add("ja");
            list.add("kk");
            //方式一
        for (int i = 0; i < list.size(); i++) {//for
        System.out.print(list.get(i) + "  ");//get():获取指定索引处的值
    }

		System.out.print("\n第2种方式：");
		for (Object object : list) {//foreach
        System.out.print(object + "  ");
    }

		System.out.print("\n第3种方式：");
    Iterator t = list.iterator();//Iterator：可以遍历集合的迭代器
		while(t.hasNext()) {//boolean hasNext():是否存在下一个元素
        System.out.print(t.next() + "  ");//E(Object) next():获得下一个元素的值
    }

		System.out.print("\n第4种方式：");
    ListIterator listIterator = list.listIterator();//ListIterator：可以遍历集合的双向迭代器
		while (listIterator.hasNext()) {//boolean hasNext():从左到右依次遍历  判断是否存在下一个元素
        System.out.print(listIterator.next() + "  ");//E(Object) next():获得下一个元素的值
    }
}
}
````

#### 5、怎么情况使用各个集合？

* 具体看场景，建议如果查询比较多则用ArrayList，增删比较多则用LinkedList

* ArrayList 随机遍历快，LinkedList添加删除快

> 本篇文章如有错的地方，欢迎在评论指正。喜欢在微信看技术文章，想要获取更多的Java、BigData资源的同学，可以**关注微信公众号：自学大数据踩的坑**

> 另，如果觉得这本篇文章写得不错，有点东西的话，各位人才记得来个三连【点赞+关注+分享】。

