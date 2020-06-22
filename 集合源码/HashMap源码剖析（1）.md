> 写博文难免会有疏漏之处，如有错误，还请各位赶紧说句话，非常感谢！

## 前言

HashMa是Java中最常用的集合类框架，也是Java语言中非常典型的数据结构，同时也是我们需要掌握的数据结构，更重要的是进大厂面试必问之一，后面附加高频率面试题。

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

#### HashMap的put()和get()的实现

##### 1、map.put(k,v)实现原理

1.首先将k,v封装到Node对象当中（节点）。

2.通过哈希算法计算出当前key的hash值

3.再通过哈希表函数/哈希算法，将hash值转换成数组的下标，下标位置上如果没有任何元素，就把Node添加到这个位置上。

> 如果说下标对应的位置上有链表。此时，就会拿着k和链表上每个节点的k进行equal。
>
> 如果所有的equals方法返回都是false，那么这个新的节点将被添加到链表的末
>
> 尾。如其中有一个equals返回了true，那么这个节点的value将会被覆盖。

```java
// 存储时:
// 这个hashCode方法这里不详述,只要理解每个key的hash是一个固定的int值即可
int hash = key.hashCode(); 
int index = hash % Entry[].length;
Entry[index] = value;
```

put方法源码解析

```java
public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
}
```

```java
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
        tab[i] = newNode(hash, key, value, null);
    else {
      //如果hash冲突了
        Node<K,V> e; K k;
      //如果给定的hash和冲突的下标中的hash的值相等并且
        if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k))))
          //那么就将已经存在的值赋给上面定义的e变量
            e = p;
       // 如果以存在的值是个树类型的，则将给定的键值对和该值关联
        else if (p instanceof TreeNode)
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
      // 如果key不相同，只是hash冲突，并且不是树，则是链表
        else {
          // 循环，直到链表中的某个节点为null，或者某个节点hash值和给定的hash值一致且key也相同，
          // 则停止循环。
            for (int binCount = 0; ; ++binCount) {
              // 如果next属性是空
                if ((e = p.next) == null) {
                  // 那么创建新的节点赋值给已有的next 属性
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
  // 如果e== null，需要增加 modeCount 变量，为迭代器服务
    ++modCount;
 	 // 如果数组长度大于了阀值
    if (++size > threshold)
     		// 重新散列
        resize();
  	// HashMap 中什么都不做
    afterNodeInsertion(evict);
    return null;
}
```

该方法可以说是 HashMap 的核心方法，该方法的步骤如下：

1、判断数组是否为空，如果是空，则创建默认长度位 16 的数组。
2、通过与运算计算对应 hash 值的下标，如果对应下标的位置没有元素，则直接创建一个。
3、如果有元素，说明 hash 冲突了，则再次进行 3 种判断。

* 判断两个冲突的key是否相等，equals 方法的价值在这里体现了。如果相等，则将已经存在的值赋给变量e。最后更新e的value，也就是替换操作。

* 如果key不相等，则判断是否是红黑树类型，如果是红黑树，则交给红黑树追加此元素。

* 如果key既不相等，也不是红黑树，则是链表，那么就遍历链表中的每一个key和给定的key是否相等。如果，链表的长度大于等于8了，则将链表改为红黑树，这是Java8 的一个新的优化。

4、最后，如果这三个判断返回的 e 不为null，则说明key重复，则更新key对应的value的值。
5、对维护着迭代器的modCount 变量加一。
6、最后判断，如果当前数组的长度已经大于阀值了。则重新hash。

> 另，如果觉得这本篇文章写得不错，有点东西的话，记得来个三连【点赞+关注+分享】。

### 2、map.get(k)实现原理

```java
//数组长度减1与运算hash值
first = tab[(n - 1) & hash]
```

> 第一步：先调用k的hashCode()方法得出哈希值，并通过哈希算法转换成数组的下标。

> 第二步：通过上一步哈希算法转换成数组的下标之后，在通过数组下标快速定位到某个位置上。
>
> 重点理解
>
> 如果这个位置上什么都没有，则返回null。
>
> 如果这个位置上有单向链表，那么它就会拿着参数K和单向链表上的每一个节点的K进行equals，如果所有equals方法都返回false，则get方法返回null。
>
> 如果其中一个节点的K和参数K进行equals返回true，那么此时该节点的value就是我们要找的value了，get方法最终返回这个要找的value。

### 3、为何随机增删、查询效率都很高的原因是？

**原因：**增删是在链表上完成的，而查询只需扫描部分，则效率高。

HashMap集合的key，会先后调用两个方法，hashCode and equals方法，这两个方法都需要重写。

### 4、为什么放在hashMap集合key部分的元素需要重写equals方法？

因为equals默认比较是两个对象内存地址

我们为什么要重写 hashcode 和 equals 方法？

因为：JDK 默认使用 Objective 类的 hashcode 方法，返回的是一个虚拟内存地址，然而每个对象的虚拟地址都是不同的，所有需要重写这两个方法。

### 5、HashMap总结

* 无序，不可重复

> 为什么是无序的？

* 因为不一定挂到哪一个单向链表上的，因此加入顺序和取出也不一样。

> 怎么保持不可重复？

* 使用equals方法来保证HashMap集合key不可重复，如key重复来，value就会覆盖。存放在HashMap集合key部分的元素，其实就是存放在HashSet集合中，则HashSet集合也需要重写equals和hashCode方法。

* hashmap集合的默认初始化容量为16，默认加载因子为0.75，也就是说这个默认加载因子是当hashMap集合底层数组的容量达到75%时，数组就开始扩容。

* hashmap集合初始化容量是2的陪数，为了达到散列均匀，提高hashmap集合的存取效率，

### 6、注意JDK8之后

JDK8之后，如果哈希表单向链表中元素超过8个，那么单向链表这种数据结构会变成红黑树数据结构。当红黑树上的节点数量小于6个，会重新把红黑树变成单向链表数据结构,官方源码如下图。

#### 问题：

如果O1和O2的hash值相同，就会存放到同一个单向链表上，

如果不同，但由于哈希算法执行结束之后转换的数组下标可能相同，此时会发上“哈希碰撞”。

HashMap的存取是采用什么算法实现？

```java
// 存储时:
// 这个hashCode方法这里不详述,只要理解每个key的hash是一个固定的int值即可
int hash = key.hashCode(); 
int index = hash % Entry[].length;
Entry[index] = value;

// 取值时:
int hash = key.hashCode();
int index = hash % Entry[].length;
return Entry[index];
```

### 7、高频面试题

* HashMap的工作原理是什么？
* HashMap中的“死锁”是怎么回事？
* HashMap中能put两个相同key吗？为什么？
* HashMap中的键值可以为空吗？原理？
* HashMap扩容机制？
* Hashmap中的链表大小超过8个时会自动转化为红黑树，当删除小于六时重新变为链表，为啥呢？



* HashMap在多线程环境下存在线程安全问题，那你一般都是怎么处理这种情况的？

答：使用Collections.synchronizedMap(Map)创建线程安全的map集合、Hashtable、ConcurrentHashMap

* Collections.synchronizedMap是怎么实现线程安全的？

答：源码是这样的，在SynchronizedMap内部它是维护了一个普通对象Map，还有排斥锁mutex，在调用这个方法的时候就需要传入一个Map，可以看到有两个构造器，如果你传入了mutex参数，则将对象排斥锁赋值为传入的对象。如果没有，则将对象排斥锁赋值为this，即调用synchronizedMap的对象，就是上面的Map，创建出synchronizedMap之后，再操作map的时候，就会对方法上锁。

8、Hashtable 跟HashMap区别？

* Hashtable 不允许键或值为 null 的，HashMap 的键值则都可以为 null，

* 初始化容量不同：HashMap 的初始容量为：16，Hashtable 初始容量为：11，两者的负载因子默认都是：0.75
* 扩容机制不同：当现有容量大于总容量 * 负载因子时，HashMap 扩容规则为当前容量翻倍，Hashtable 扩容规则为当前容量翻倍 + 1
* 迭代器不同：HashMap 中的 Iterator 迭代器是 fail-fast（**速失败机制**）迭代器，JDK8之前的版本中，Hashtable是没有fast-fail机制，在JDK8及以后版本中才加入fast-fail的。
* 继承的父类不同：HashMap是继承自AbstractMap类，而HashTable是继承自Dictionary类。![这里写图片描述](https://img-blog.csdn.net/20180306020714182?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2FuZ3hpbmcyMzM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

* 线程安全性不同：Hashtable是线程安全，HashMap不是线程安全，在多线程并发的环境下，可能会产生死锁等问题

9、接着上 面问题说一下，为什么 Hashtable 不允许 key 和value为 null，而 HashMap 则可以呢？

因为Hashtable在我们put 空值的时候会直接抛空指针异常，直接上源码一目了然。

