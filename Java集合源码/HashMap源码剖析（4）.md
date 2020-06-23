> 水平有限，难免会有疏漏之处，如有错误，还请指出，感谢！

### 前言

本文能够让你读懂HashMap的put方法源码和实现原理、JDK1.7和JDK1.8存储区别及哈希冲突如何解决。

#### 数组特点

* 存储区间是连续，且占用内存严重，空间复杂也很大，时间复杂为O(1)。

* 优点：是随机读取效率很高，原因数组是连续（随机访问性强，查找速度快）。
* 缺点：插入和删除数据效率低，因插入数据，这个位置后面的数据在内存中要往后移的，且大小固定不易动态扩展。

#### 链表特点

* 区间离散，占用内存宽松，空间复杂度小，时间复杂度O(N)。

* 优点：插入删除速度快，内存利用率高，没有大小固定，扩展灵活。
* 缺点：不能随机查找，每次都是从第一个开始遍历（查询效率低）。

#### 哈希表特点

以上数组和链表，大家都知道各自优缺点。那么我们能不能把以上两种结合一起使用，从而**实现查询效率高和插入删除效率也高的数据结构**呢？答案是可以滴，那就是**哈希表**可以满足，接下来我们一起复习HashMap中的put()和get()方法实现原理。

千言万语，废话不多说，直接上源码。

#### 1、先知道HashMap的重要的几个属性

```java
//默认的初始容量，为16-必须为2的幂。
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16

//当容量被占满0.75时就需要reSize扩容
static final float DEFAULT_LOAD_FACTOR = 0.75f;

//链表长度到8，就转为红黑树
static final int TREEIFY_THRESHOLD = 8;

// 树大小为6，就转回链表
static final int UNTREEIFY_THRESHOLD = 6;

//哈希桶数组
transient Node<K,V>[] table;
```

#### 2、HashMap的put方法（JDK1.8）

![image-20200504113855509](/Users/dimitri/Library/Application Support/typora-user-images/image-20200504113855509.png)

#### 3、put方法源码（JDK1.8）

```java
//hash函数，了解即可
//该方法是把key与其高16位异或
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
//put方法源码
public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
}
```

```java
//put方法具体实现
//方法中参数onlyIfAbsent表示是否替换原值
//参数evict是区别通过put添加还是创建时初始化数据
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
               boolean evict) {
    Node<K,V>[] tab; Node<K,V> p; int n, i;
  	//如果对象数组是null或数组长度为0，则需要进行初始化数组
    if ((tab = table) == null || (n = tab.length) == 0)
      	//获取数组长度
        n = (tab = resize()).length;
  	//如果通过hash值计算出来的数组下标那个地方没有元素,
  	//则根据给定key and value创建一个元素
    if ((p = tab[i = (n - 1) & hash]) == null)
      	// 将元素直接插进去
        tab[i] = newNode(hash, key, value, null);
    else {
      	//如果hash冲突了
     	 //e是用来查看是不是待插入的元素已经有了，有就替换
        Node<K,V> e; K k;
      	//如果给定的hash和冲突的下标中的hash的值相等并且
        if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k))))
          //那么就将已经存在的值赋给上面定义的e变量
            e = p;
       // 如果已存在的值是个树类型的
        else if (p instanceof TreeNode)
          	//如果已存在的值是个树类型的，把节点添加到树中
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
        else {
          	//现在是链表结构，然后把待插入元素挂在链表末尾
            for (int binCount = 0; ; ++binCount) {
              // 如果next属性是空
                if ((e = p.next) == null) {
                  // 那么创建新的节点赋值给已有的next属性
                    p.next = newNode(hash, key, value, null);
                   // 如果树的阀值大于等于7，也就是，链表长度达到了8（从0开始）
                    if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                      // 如果链表长度达到了8，且数组长度小于64，那么就重新散列，
                      // 如果大于64，则创建红黑树
                        treeifyBin(tab, hash);
                    break;
                }
              // 如果hash值和next的hash值相同且（key也相同）
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    break;
              // 如果给定的hash值不同或者key不同。
              // 将next值赋给p，为下次循环做铺垫
                p = e;
            }
        }
       // 通过上面的逻辑，如果e不是null，表示：该元素存在了(也就是他们呢key相等)
        if (e != null) { // existing mapping for key
          	// 取出该元素的值
            V oldValue = e.value;
         	 // 如果 onlyIfAbsent 是 true，就不要改变已有的值，这里我们是false。
          	// 如果是false，或者 value 是null
            if (!onlyIfAbsent || oldValue == null)
             	 // 将新的值替换老的值
                e.value = value;
            // HashMap 中什么都不做
            afterNodeAccess(e);
         	 // 返回之前的旧值
            return oldValue;
        }
    }
  // 如果e== null。需要增加modeCount 变量，为迭代器服务
    ++modCount;
 	 // 如果数组长度大于了阀值，达到了capacity的0.75，需要扩容
    if (++size > threshold)
     		// 重新散列
        resize();
  	// 默认空的
    afterNodeInsertion(evict);
    return null;
}
```

#### 4、以上是HashMap的核心方法，总结该方法的实现步骤

第一步：首先判断数组是否为null，如果为null，则创建默认长度位16的数组。

第二步：通过hashCode算法计算出对应hash值的下标，如果对应下标的位置没有元素，则直接创建一个。

第三步：假如下标位置有元素，说明hash冲突了，则再次进行以下3种判断。

* 首先判断两个冲突的key是否相等，使用equals 方法进行比较，如果相等，则将已经存在的值赋给变量e。最后更新e的value覆盖掉。

* 如果key不相等，则判断是否是红黑树类型，如果是红黑树，则交给红黑树追加此元素。

* 如果key既不相等，也不是红黑树，则是链表，那么就遍历链表中的每一个key和给定的key是否相等。如链表的长度大于等于8了，则将链表改为红黑树，这里就是体现JDK1.8的新的一个优化。

第四步：如果这三个判断返回的e不为null，则说明key重复了，则更新key对应的value的值。
第五步：其对维护着迭代器的modCount 变量加一。
第六步：最后判断，如果当前数组的长度已经大于阀值了，则会重新hash（重新散列）。

#### 5、了解存取算法源码

```java
// 存储时算法
// 这个hashCode方法,先理解每个key的hash是一个固定的int值即可
int hash = key.hashCode(); 
int index = hash % Entry[].length;
Entry[index] = value;

// 取值时算法
int hash = key.hashCode();
int index = hash % Entry[].length;
return Entry[index];
```

#### 6、解决Hash冲突有哪些方法？

解决哈希冲突方法有：链地址法也叫拉链法，当然还有开放定址法、再哈希法、建立公共溢出区还有很多方法，今天就拿“拉链法”来讲解。

##### 拉链法（JDK1.7）

拉链法其实是将哈希值相同的元素构成单链表，然后将单链表的头指针存放在哈希表的第i个单元中，如查找、插入和删除主要在同一个单链表中进行。

##### 拉链法（JDK1.8后）

JDK1.8之后将链表长度大于8的，会转换为红黑树存储了，底层实现由之前的 【数组+链表】 变为【数组+链表+红黑树】（注意：当链表节点小于8仍然是以链表存在，但链表节大于8时会转为红黑树）。

#### 7、HashMap的两种遍历方式

首先了解**Map接口常用方法**

````java
java.util.Map接口常用方法
    1、map和collection没有继承关系
    2、map集合以key和value的方式存储数据
        key and value都是引用数据类型、都是存储对象的内存地址
    3、map接口常用方法
        v put(k key,v value)向map集合添加键值对
        v get(object key)通过key获取value
        void clear()清空map集合
        boolean containsKey(Object key)判断map中是否包含某个key
        boolean containsValue(Object value)判断map中是否包含某个value
        Set<k> keySet()获取map集合所有的key
        V remove(Object key)通过key删除键值对
        int size()获取map集合中键值对
        Collection<V> value()获取map集合中所有的value，返回一个collection
        Set<Map.Entry<k,v>> entrySet()将map集合转换成Set集合
        System.out.println(map.isEmpty());//判断是否为空
````

````java
public class MapTest01 {
    public static void main(String[] args) {
        Map<Integer,String> map = new HashMap();
        map.put(1,"kobe");
        map.put(2,"乔丹");
        map.put(3,"詹姆斯");
        System.out.println(map.get(3));
        map.containsKey(1);
        //第一种遍历方式
        Set<Integer> keySet = map.keySet();
        for (Integer key :keySet) {
            System.out.println(key + "=" + map.get(key));
        }
        //第二种遍历方式
        Set<Map.Entry<Integer, String>> set = map.entrySet();
        for (Map.Entry<Integer,String> node : set){
            System.out.println(node.getKey() + "=" + node.getValue());
        }
    }
}
````

### 为什么要重写 hashcode 和 equals 方法？

因为：JDK 默认使用 Objective 类的 hashcode 方法，返回的是一个虚拟内存地址，然而每个对象的虚拟地址都是不同的，所有需要重写这两个方法。

> 另，如果觉得这本篇文章写得不错，有点东西的话，记得来个三连【点赞+关注+分享】。

