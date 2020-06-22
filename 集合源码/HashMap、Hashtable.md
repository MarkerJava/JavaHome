### 前言

快读懂Hashtable源码，首先要来看了解的几个属性，这样到后面深入源码，你才不会觉得这些属性陌生，这些个属性也是面试常出现的。

> 声明，本文用的是jdk1.8+

> 注：源码解析部分比较细，建议放慢速度一步一步去理解

一、HashMap源码解析

HashMap特点：

* 无序，允许为null，线程不安全

* 底层由散列表(哈希表)实现，也就是数组+链表+红黑树（JDK1.8）

* 初始容量和装载因子对HashMap影响大

HashMap的几个重要属性

````java
		//初始容量为16
		//1 << 4的幂(2的4次方)
    static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16
		
		//最大容量为2147483648
		//1 << 30的幂(2的31次方)=
    static final int MAXIMUM_CAPACITY = 1 << 30;

		//默认装载因子
    static final float DEFAULT_LOAD_FACTOR = 0.75f;

    //将元素添加到至链表长度到8，且散列表容量大于64，就转为红黑树（两个都有满足）
    static final int TREEIFY_THRESHOLD = 8;

    //当树大小为6，就转换成链表
    static final int UNTREEIFY_THRESHOLD = 6;

    //桶可能转化为树形结构的最小容量
    static final int MIN_TREEIFY_CAPACITY = 64;
		//哈希桶数组
		transient Node<K,V>[] table;
		//是指更改HashMap中的映射数或以其他方式修改其内部结构（例如重新哈希）的修改。此字段用于使HashMap的Collection-view上的迭代器快速失败
		//这个成员变量是阈值，决定了是否要将散列表再散列
		int threshold;
````

HashMap类中的一个Node<K,V>静态内部类

Node它实现了Map.Entry接口，本质是就是一个映射也就是键值对，如下面的public final K getKey()        { return key; }就是一个Node对象。

````java
//Node是HashMap的一个静态内部类，实现了Map.Entry<K,V>接口
//链表存储结构，也就是以HashMap.Node<K,V>类的对象方式存储的
static class Node<K,V> implements Map.Entry<K,V> {
        final int hash;//hash值
        final K key;//键
        V value;//值
        Node<K,V> next;//下一个节点

        Node(int hash, K key, V value, Node<K,V> next) {
            this.hash = hash;
            this.key = key;
            this.value = value;
            this.next = next;
        }

        public final K getKey()        { return key; }
        public final V getValue()      { return value; }
        public final String toString() { return key + "=" + value; }
				//返回key的hashCode值和value的hashCode值进行异或运算结果
        public final int hashCode() {
            return Objects.hashCode(key) ^ Objects.hashCode(value);
        }
				// 实现接口定义的方法，且该方法不可被重写
        // 设值，返回旧值
        public final V setValue(V newValue) {
            V oldValue = value;
            value = newValue;
            return oldValue;
        }
				//判断相等的依据是，只要是Map.Entry的一个实例，并且键键、值值都相等就返回True
        public final boolean equals(Object o) {
            if (o == this)
                return true;
            if (o instanceof Map.Entry) {
                Map.Entry<?,?> e = (Map.Entry<?,?>)o;
                if (Objects.equals(key, e.getKey()) &&
                    Objects.equals(value, e.getValue()))
                    return true;
            }
            return false;
        }
    }
````

**注：Node类里面存在着Objects.equals、Objects.hashCode这两个静态方法，具体作用是帮助用户节省空值判断和指针相关判断的。**

````java
public static int hashCode(Object o) {
        return o != null ? o.hashCode() : 0;
    }
public static boolean equals(Object a, Object b) {
        return (a == b) || (a != null && a.equals(b));
    }
````

理解为什么要有HashMap的hash()方法？

hash值的计算方法

````java
static final int hash(Object key) {
        int h;
  			//h = key.hashCode()) ^ (h >>> 16)是得到key的HashCode，和ke yHashCode的高16位则做异或运算的。
        return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
    }
注：h >>> 16，表示无符号右移16位，高位补0，任何数跟0异或都是其本身
````

大家可以看出，hash算法长成怎么样子了，如果key的hash值高16位不变，低16位与高16位异或作为key的最终hash值

HashMap的核心方法put()

```java
//该方法，内部调用putVal()方法的，是以hash算法计算哈希值，传入key和value
public V put(K key, V value) {
        return putVal(hash(key), key, value, false, true);
    }
//putVal()方法
//方法中参数onlyIfAbsent表示是否替换原值
//参数evict是区别通过put添加还是创建时初始化数据
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
               boolean evict) {
    Node<K,V>[] tab; Node<K,V> p; int n, i;
  	//第一步：
  	//判断如果散列表为null或长度为0，则需要进行初始化数组
    if ((tab = table) == null || (n = tab.length) == 0)
        n = (tab = resize()).length;
  	//第一种：hash不冲突的情况下
 	 //如果通过hash值计算出来的数组下标那个地方没有元素,没有直接添加元素到列表 
    if ((p = tab[i = (n - 1) & hash]) == null)
      	// 将元素直接添加到散列表
        tab[i] = newNode(hash, key, value, null);
    else {
      	//第二种：hash冲突的情况下，怎么办？
     	 //注：e是用来查看是不是待插入的元素已经有了，有就替换
        Node<K,V> e; K k;
      	//如果给定的hash和冲突的下标中的hash的值相等并且
      	//也就是即将插入的元素，桶的hash和key都相等了，就把它记录下来
        if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k))))
          //那么就将已经存在的值赋给上面定义的e变量
            e = p;
        else if (p instanceof TreeNode)
          	//第二步：
          	//如果已存在的值是个红黑树类结构，就把该节点添加到红黑树的插入方法中
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
        else {
          	//通过第二步，已经确定为红黑树了接下来
          	//现在是链表结构，然后把待插入元素挂在链表末尾
            for (int binCount = 0; ; ++binCount) {
              // 如果next属性是空
                if ((e = p.next) == null) {
                  // 那么创建新的节点赋值给已有的next属性
                    p.next = newNode(hash, key, value, null);
                   // 如果树的阀值大于，链表长度8（从0开始），就转成红黑树
                    if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st

                        treeifyBin(tab, hash);
                    break;
                }
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    break;
                p = e;
            }
        }
      	//新值覆盖旧值，并返回旧值
        if (e != null) { // existing mapping for key
            V oldValue = e.value;
         	 // 如果 onlyIfAbsent 是 true，就不要改变已有的值，这里我们是false。
          	// 如果是false，或者 value 是null
            if (!onlyIfAbsent || oldValue == null)
             	 // 将新的值替换老的值
                e.value = value;
            afterNodeAccess(e);
         	 // 返回之前的旧值
            return oldValue;
        }
    }
  // 如果e== null，需要增加modeCount变量，为迭代器服务
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

**注**：具体hash算法计算参考前面那一步

存储过程是根据key的哈希值来保存在散列表中的，默认的初始容量是16，如果你要放到散列表中，也就是0-15的位置上tab[i = (n - 1) & hash]`。但是大家可能发现的是：它在做`&`运算的时候，仅仅是后4位有效~那如果我们key的哈希值高位变化很大，低位变化很小。直接拿过去做`&`运算，这就会导致计算出来的Hash值相同的很多。

然而设计者将key的哈希值的高位也做了运算，也就是低位是高位与低位的结合，这就增加了随机性，同时减少了碰撞冲突的可能性。

二、HashMap与Hashtable区别

* Hashtable其实和HashMap的最大的区别是它是线程安全的，另外它不允许key和value为null。
* 底层数组结构不同：Hashtable为【数组+链表】，而HashMap为数组+链表+红黑树(JDK1.8)
* 继承方式不同：Dictionary，而HashMap继承AbstractMap
* 初始容量不同：Hashtable默认初始容量为11，而HashMap初始容量为16
* 扩容方式不同：Hashtable是原始容量x2，而HashMap是原始容量x2+1

总结

1. 在开发中，尽量不要在并发的环境中同时操作HashMap，建议使用ConcurrentHashMap。

2. 初始容量的默认值是16，如果我们初始容量设置过大，会受影响遍历时的速度；初始容量过小，扩容的次数有可能变得多，多次扩容会耗费性能。所以在使用HashMap的时候，估算map的大小，初始化的时候给一个大致的数值，避免map进行频繁的扩容。

3. 对于默认装载因子为0.75，要是我们设置装载因子初始值小了，扩容的次数可能就会变多，但是以减小散列冲突的可能性；如果设置过大，可能会导致散列冲突的可能性变大，如果冲突了就进行【红黑树】，从而会影响性能，但是可以减少散列表再扩容的次数。

4. HashMap的缺点:在多线程环境下，HashMap不是线程安全，操作HashMap会导致各种各样的线程安全问题，比如在HashMap扩容重哈希时出现的死循环问题，脏读问题等。

5. 总的来说HashMap是一个数组链表，当一个key/Value对被加入时，首先会通过Hash算法定位出这个键值对要被放入的桶，然后就把它插到相应桶中。如果这个桶中已经有元素了，那么发生了碰撞，这样会在这个桶中形成一个链表。一般来说，当有数据要插入HashMap时，都会检查容量是否超过设定的阈值（thredhold），如果超过，则需要增大HashMap的容量，但是这样一来，就需要对整个HashMap里的节点进行重哈希操作。注：HashMap线程不安全的典型表现 —— **死循环**。

> 本篇文章如有错的地方，欢迎在评论指正。喜欢在微信看技术文章，想要获取更多的Java、BigData资源的同学，可以**关注微信公众号：自学大数据踩的坑**

> 另，如果觉得这本篇文章写得不错，有点东西的话，各位人才记得来个三连【点赞+关注+分享】。下一遍讲解ConcurrentHashMap集合，敬请期待哦。。。

