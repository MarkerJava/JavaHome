### 前言

学习ConcurrentHashMap之前，我们还是先来复习一下**CAS、volatile、乐观锁与悲观锁**的知识吧

> 本文使用JDK1.8+

### 一、CAS算法介绍

* CAS（Compare and swap）比较与交换， 是一种有名的**无锁算法**，CAS的3个操作数
  * 内存值V
  * 旧的预期值A
  * 要修改的新值B

**当且仅当内存值V和预期值A相同时，就将内存值V修改为新值B，否则不做任何操作**

- 当多个线程尝试使用CAS同时更新同一个变量时，只有其中一个线程能更新变量的值(**A和内存值V相同时，将内存值V修改为B)**，而其它线程都失败，失败的线程**并不会被挂起**，而是被告知这次竞争中失败，并可以再次尝试(否则什么都不做)
- 缺点：CAS操作是先取出内存值，然后才将内存值与期望值比较的，这样可能会出现这样一个问题，假如线程1获取了内存值V，然后线程2也从内存中取出V，并且线程2进行了一些修改操作，将内存值修改为B，然后two又将内存值修改为V，当前线程的CAS操作无法分辨内存值V是否发生过变化，所以尽管CAS成功，但可能存在潜在的问题。解决使用AtomicStampedReference和AtomicMarkableReference实现标记的功能。

小结：先**比较**是否相等，如果相等则**替换**(CAS算法)

CAS是项**乐观锁**技术，当多个线程尝试使用CAS同时更新同一个变量时，只有其中一个线程能更新变量的值，而其它线程都失败，失败的线程并不会被挂起，而是被告知这次竞争中失败，并可以再次尝试。

**CAS无锁算法的C实现**

````java
int compare_and_swap (int* reg, int oldval, int newval) 
{
  ATOMIC();
  int old_reg_val = *reg;
  if (old_reg_val == oldval) 
     *reg = newval;
  END_ATOMIC();
  return old_reg_val;
}
//CAS比较与交换的伪代码可以表示为
do{  
       备份旧数据； 
       基于旧数据构造新数据； 
}while(!CAS( 内存地址，备份的旧数据，新数据 ))  
  //假设有这样一种场景，一个CPU去更新一个值，但如果想改的值不再是原来的值，操作就失败，因为很明显，有其它操作先改变了这个值。
  //就是指当两者进行比较时，如果相等，则证明共享数据没有被修改，替换成新值，然后继续往下运行；如果不相等，说明共享数据已经被修改，放弃已经所做的操作，然后重新执行刚才的操作。容易看出 CAS 操作是基于共享数据不会被修改的假设，采用了类似于数据库的 commit-retry 的模式。当同步冲突出现的机会很少时，这种假设能带来较大的性能提升。
````

### 二、volatile介绍

volatile变量是一个更轻量级的同步机制，因为在使用这些变量时不会发生上下文切换和线程调度等操作，但是volatile变量也存在一些局限，比如不能用于构建原子的复合操作，因此当一个变量依赖旧值时就不能使用volatile变量，volatile内部已经做了synchronized。

**volatile只能保证变量对各个线程的可见性，但不能保证原子性**

* 保证该变量对所有线程的可见性
  * 在多线程的环境下：当这个变量修改时，**所有的线程都会知道该变量被修改了**，也就是所谓的“可见性”

* 不保证原子性
  * 修改变量(赋值)实质上是在JVM中分了好几步，而在这几步内(从装载变量到修改)，它是不安全的.
  * 就是一次操作，要么完全成功，要么完全失败。

### 三、乐观锁与悲观锁

独占锁是一种悲观锁，synchronized就是一种独占锁，它假设最坏的情况，并且只有在确保其它线程不会造成干扰的情况下执行，会导致其它所有需要锁的线程挂起，等待持有锁的线程释放锁。而另一个更加有效的锁就是乐观锁。所谓乐观锁就是，每次不加锁而是假设没有冲突而去完成某项操作，如果因为冲突失败就重试，直到成功为止。

生活案例：

1，总是假设最坏的情况，每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会阻塞直到它拿到锁（**共享资源每次只给一个线程使用，其它线程阻塞，用完后再把资源转让给其它线程**）。传统的关系型数据库里边就用到了很多这种锁机制，比如行锁，表锁等，读锁，写锁等，都是在做操作之前先上锁。Java中`synchronized`和`ReentrantLock`等独占锁就是悲观锁思想的实现。2，总是假设最好的情况，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，可以使用版本号机制和CAS算法实现。**乐观锁适用于多读的应用类型，这样可以提高吞吐量**，像数据库提供的类似于**write_condition机制**，其实都是提供的乐观锁。在Java中`java.util.concurrent.atomic`包下面的原子变量类就是使用了乐观锁的一种实现方式CAS实现的。

乐观锁与悲观锁

1、乐观锁适用于多读场景下

2、悲观锁适用于多写场景下

乐观锁常见的两种实现方式

乐观锁一般会使用版本号机制或CAS算法实现。

CAS与synchronized的使用情景

CAS适用于写比较少的情况下（多读场景，冲突一般较少），synchronized适用于写比较多的情况下（多写场景，冲突一般较多）

### 四、Java中的原子操作( atomic operations)

原子操作指的是在一步之内就完成而且不能被中断，原子操作在多线程环境中是线程安全的，无需考虑同步的问题。

> 学习ConcurrentHashMap之前，先了解JDK1.7与JDK1.8的区别，本文使用JDK1.8+

### 五、JDK1.7和JDK1.8区别

#### 1、JDK1.7

JDK1.7中ConcurrentHashMap是通过“锁分段”来实现线程安全的。啥？什么是“锁分段”？其实就是ConcurrentHashMap将哈希表分成许多片段（segments），每个片段（table）都类似于HashMap，它有一个HashEntry数组，数组的每项又是HashEntry组成的链表。每个片段都是Segment类型的。

**核心存储结构是segment，它是继承ReentrantLock的源码如下**：

````java
static class Segment<K,V> extends ReentrantLock implements Serializable {
        private static final long serialVersionUID = 2249069246763182397L;
        final float loadFactor;
        Segment(float lf) { this.loadFactor = lf; }
    }
````

所以Segment本质上是一个可重入的**互斥锁**，这样每个片段都有了一个锁，这就是“锁分段”。

**注**：线程如想访问某一key-value键值对，需要先获取键值对所在的segment的锁，获取锁后，其他线程就不能访问此segment了，但可以访问其他的segment。

#### 2、JDK1.8

在JDK1.8中，ConcurrentHashMap没有用“锁分段”来实现线程安全，而是使用**CAS算法**和**synchronized**来确保线程安全，但是底层segment并没有被删除的。

### 六、ConcurrentHashMap的重要内部类及构造方法

#### 1、重要内部类

````java
//基本的内部类
static class Node<K,V> implements Map.Entry<K,V> {......}
//红黑树节点，供TreeBins使用
static final class TreeNode<K,V> extends Node<K,V> {......}
//红黑树结构,该类并不包装key-value键值对，而是TreeNode的列表和它们的根节点,这个类含有读写锁
static final class TreeBin<K,V> extends Node<K,V> {......}
//是包含一个nextTable指针，和find方法 
static final class ForwardingNode<K,V> extends Node<K,V> {......}
````

#### 2、构造方法

````java
//第一个构造器
//使用默认的初始表大小（16）创建一个新的容量
//构造一个具有默认初始容量(16)、默认负载因子(0.75)和默认并发级别(16)的空ConcurrentHashMap
public ConcurrentHashMap() {
    }
//第二个构造器
//该构造函数是在构造一个具有指定容量、默认负载因子(0.75)和默认并发级别(16)的空ConcurrentHashMap
public ConcurrentHashMap(int initialCapacity) {
        this(initialCapacity, LOAD_FACTOR, 1);
    }
//第三个构造器
//构造一个与指定 Map 具有相同映射的 ConcurrentHashMap，其初始容量不小于 16 (具体依赖于指定Map的大小)，负载因子是 0.75，并发级别是 16， 是 Java Collection Framework 规范推荐提供的
public ConcurrentHashMap(Map<? extends K, ? extends V> m) {。。。。。。}
//第四个构造器
//该构造函数是在构造一个具有指定容量、指定负载因子和默认并发级别(16)的空ConcurrentHashMap
public ConcurrentHashMap(int initialCapacity, float loadFactor) {
        this(initialCapacity, loadFactor, 1);
    }
//第五个构造器
//该构造函数是在构造一个具有指定容量、指定负载因子和指定段数目/并发级别(若不是2的幂次方，则会调整为2的幂次方)的空ConcurrentHashMap
public ConcurrentHashMap(int initialCapacity,
                             float loadFactor, int concurrencyLevel) {。。。。。。}
````

### 七、ConcurrentHashMap核心方法

#### 1、put方法

其实只要读懂之前HashMap的方法就轻松的，基本类似的，只不过ConcurrentHashMap多加几个方法而已。

````java
public V put(K key, V value) {
    return putVal(key, value, false);
}
//实现方法
final V putVal(K key, V value, boolean onlyIfAbsent) {
  	//键值对都不允许为空，如果为空就NullPointerException()
    if (key == null || value == null) throw new NullPointerException();
    //计算哈希值
  	//spread=(h ^ (h >>> 16)) & HASH_BITS;保证了hash >= 0
    int hash = spread(key.hashCode());
    int binCount = 0;
    //死循环，只有插入成功才能跳出循环
    for (Node<K,V>[] tab = table;;) {
        Node<K,V> f; int n, i, fh;
        //如果table没有初始化，初始化table
        if (tab == null || (n = tab.length) == 0)
            tab = initTable();
        //根据哈希值计算在table中的位置
      	//先1，Node<K,V>(hash, key, value, null)
      	//后2，casTabAt
        else if ((f = tabAt(tab, i = (n - 1) & hash)) == null) {
            //如果这个位置没有值，直接将键值对放进去，不需要加锁
            if (casTabAt(tab, i, null,
                         new Node<K,V>(hash, key, value, null)))
              //退出，如果直接执行addCount(1L, binCount);  
              //说明节点不是被替换的，是被插入的，则将map的元素数量加1
              break;                   // no lock when adding to empty bin
        }
      
        //如果要插入的位置，MOVED为-1，则头节点为forwordingNode节点，表明该位置正在进行扩容
        //如果tab[i]不为空并且hash值为MOVED，
        //说明该链表正在进行transfer操作，返回扩容完成后的table
        else if ((fh = f.hash) == MOVED)
            tab = helpTransfer(tab, f);
        else {
            V oldVal = null;
            //这一步，说明要插入的位置有值，需要加锁
            synchronized (f) {
                //确定f是tab中的头节点
                if (tabAt(tab, i) == f) {
                    //如果头结点的哈希值大于等于0，说明要插入的节点在链表中
                    if (fh >= 0) {
                        binCount = 1;
                        //遍历链表中的所有节点
                        for (Node<K,V> e = f;; ++binCount) {
                            K ek;
                            //如果某一节点的key哈希值和key与参数相等，替换节点的value
                            if (e.hash == hash &&
                                ((ek = e.key) == key ||
                                 (ek != null && key.equals(ek)))) {
                                oldVal = e.val;
                                if (!onlyIfAbsent)
                                    e.val = value;
                                break;
                            }
                            Node<K,V> pred = e;
                            //遍历到了最后一个节点，还没找到key对应的节点，根据参数新建节点，插入链表尾部
                            if ((e = e.next) == null) {
                                pred.next = new Node<K,V>(hash, key,
                                                          value, null);
                                break;
                            }
                        }
                    }
                  //如果首节点为TreeBin类型，说明为红黑树结构，执行putTreeVal操作
                    else if (f instanceof TreeBin) {
                        Node<K,V> p;
                        binCount = 2;
                        if ((p = ((TreeBin<K,V>)f).putTreeVal(hash, key,
                                                       value)) != null) {
                            oldVal = p.val;
                            if (!onlyIfAbsent)
                                p.val = value;
                        }
                    }
                }
            }
            //判断链表是否需要转变为树
            if (binCount != 0) {
                //判断节点数量是否>=8，如果大于就需要把链表转化成红黑树
                if (binCount >= TREEIFY_THRESHOLD)
                    treeifyBin(tab, i);
                if (oldVal != null)
                  	//若key存在则返回旧的value值
                    return oldVal;
              	// 若key不存在，则说明构造了一个新节点，跳出循环，调用addCount
                break;
            }
        }
    }
  	
    //能执行到这一步，说明节点不是被替换的，是被插入的
  	//所以将map的元素数量增加1，有可能触发transfer操作(扩容)
    addCount(1L, binCount);
    return null;
}

//addCount方法，table结构使用了synchronized,有兴趣可以进入源码查看，这里就不一一罗列了
private final void addCount(long x, int check) {。。。。。。}
````

**ConcurrentHashMap的put方法实现步骤**；

1、首先，判断key和value是否为null，其中一个为null，则抛出NullPointerException()。

> 注：ConcurrentHashMap的key和Value都不能为null

2、计算哈希值：spread(key.hashCode());

3、根据哈希值计算放在table中的位置

4、通过哈希值执行插入或替换操作

* 如果这个位置没有值，直接将键值对放进去，不需要加锁
* 如果要插入的位置是一个forwordingNode节点，表示正在扩容，那么当前线程帮助扩容
  3.3 加锁。以下操作都需要加锁
* 如果要插入的节点在链表中，遍历链表中的所有节点，如果某一节点的key哈希值和key与参数相等，替换节点的value，记录被替换的值；如果遍历到了最后一个节点，还没找到key对应的节点，根据参数新建节点，插入链表尾部
* 如果要插入的节点在树中，则按照树的方式插入或替换节点。如果是替换操作，记录被替换的值

5、判断如果节点数量是大于8，就将链表转化成红黑树（binCount >= TREEIFY_THRESHOLD）

6、如果操作3中执行的是替换操作，返回被替换的value，然后程序结束

7、如果能执行到这一步，说明节点不是被替换的，是被插入的，所以要将map的元素数量加1

为什么ConcurrentHashMap效率高于HashTable？

从上面源码已经了解了ConcurrentHashMap，它通过在**链表上加锁**来实现同步的。则看出ConcurrentHashMap其实就多增加了锁的个数，效率效率就提高；而HashTable是通过在每个方法上加Synchronized来实现同步的，这样使得效率略低于ConcurrentHashMap的原因。

#### 1.1 针对put方法里面的【扩容】知识

````java
    final Node<K,V>[] helpTransfer(Node<K,V>[] tab, Node<K,V> f) {
        Node<K,V>[] nextTab; int sc;
三个判断条件判断的是扩容是否结束，ForwardingNode再创建时持有nextTable数组的引用，
nextTable会在扩容结束后被置为null。
        if (tab != null && (f instanceof ForwardingNode) &&
            (nextTab = ((ForwardingNode<K,V>)f).nextTable) != null) {
            int rs = resizeStamp(tab.length); // 本次扩容的标识，数组大小不变则rs不变
循环的这些判断条件为tue的话表明扩容未结束
            while (nextTab == nextTable && table == tab &&
                   (sc = sizeCtl) < 0) {  扩容时sizeCtl一定小于0
1，校验标识，resizeStamp的参数大小不变则值相等
2，sc == rs + 1 说明最后一个扩容线程正在执行收尾工作，你没必要来帮忙了。
3，sc == rs + MAX_RESIZERS 说明扩容线程数超过最大值
4，transferIndex <= 0在上面分析过，扩容中transferIndex表示最近一个被分配的stride区域的下边界，
<=0代表数组被分配完了
                if ((sc >>> RESIZE_STAMP_SHIFT) != rs || sc == rs + 1 ||
                    sc == rs + MAX_RESIZERS || transferIndex <= 0)
                    break;
cas配合while循环构成自旋CAS，是线程安全的保证。将sizeCtl+1，
sizeCtl此时的低16位为N=扩容线程数+1
                if (U.compareAndSwapInt(this, SIZECTL, sc, sc + 1)) {
                    transfer(tab, nextTab);
                    break;
                }
            }
            return nextTab;
        }
        return table;
    }
````

#### 2、get方法

````java
public V get(Object key) {
        Node<K,V>[] tab; Node<K,V> e, p; int n, eh; K ek;
        int h = spread(key.hashCode());
  			//如果表不为空，表长度大于0，key所在的桶的头结点不为null
        if ((tab = table) != null && (n = tab.length) > 0 &&
            (e = tabAt(tab, (n - 1) & h)) != null) {
          	//如果查到的桶的头结点的key哈希值与参数key的哈希值相同
            if ((eh = e.hash) == h) {
              	//如果查到的桶的头结点的key参数key相等，返回桶的头结点的value
                if ((ek = e.key) == key || (ek != null && key.equals(ek)))
                    return e.val;
            }
            else if (eh < 0)
              	//如果查到的桶的头结点的key哈希值小于0，即要找的在树上
                return (p = e.find(h, key)) != null ? p.val : null;
            while ((e = e.next) != null) {
              	//如果要找的节点既不是桶的头结点也不是在树上，那就说明在链表上
                if (e.hash == h &&
                    ((ek = e.key) == key || (ek != null && key.equals(ek))))//遍历链表，找到节点，返回值
                    return e.val;
            }
        }
  			//啥也没有找到，就返回个null值吧
        return null;
    }
可以将步骤总结如下：

通过key计算哈希值
通过哈希值找到桶
通过哈希值和桶来查找节点
3.1. 以此判断桶的头结点是不是要找的节点
3.2. 如果不是，判断桶的头节点的哈希值是否小于0，如果是则说明要找的节点在树上
3.3. 如果以上两个条件都不满足，则说明要找的节点在链表上，遍历链表，查找节点
如果通过以上步骤找到了节点，返回节点的value。没找到，就返回null。

````

#### 八、为什么在高并发的情况下高效？

ConcurrentHashMap的源代码会涉及到散列算法和链表数据结构，所以，读者需要对散列算法和基于链表的数据结构有所了解，特别是对HashMap的进一步了解和回顾。

ConcurrentHashMap中，无论是读操作还是写操作都能保证很高的性能：在进行读操作时(几乎)不需要加锁，而在写操作时通过锁分段技术只对所操作的段加锁而不影响客户端对其它段的访问。特别地，在理想状态下，ConcurrentHashMap 可以支持 16 个线程执行并发写操作（如果并发级别设为16），及任意数量线程的读操作。


ConcurrentHashMap本质上是一个Segment数组，而一个Segment实例又包含若干个桶，每个桶中都包含一条由若干个 HashEntry 对象链接起来的链表

ConcurrentHashMap的高效并发机制是通过以下三方面来保证的

- 通过锁分段技术保证并发环境下的写操作
- 通过 HashEntry的不变性、Volatile变量的内存可见性和加锁重读机制保证高效、安全的读操作；
- 通过不加锁和加锁两种方案控制跨段操作的的安全性。

参考资料：

[https://blog.csdn.net/panweiwei1994/article/details/78897275](https://blog.csdn.net/panweiwei1994/article/details/78897275)

[https://blog.csdn.net/u010723709/article/details/48007881](https://blog.csdn.net/u010723709/article/details/48007881)

[https://blog.csdn.net/melod_bc/article/details/54150679](https://blog.csdn.net/melod_bc/article/details/54150679)

[https://www.jianshu.com/p/e694f1e868ec](https://www.jianshu.com/p/e694f1e868ec)

> 本篇文章如有错的地方，欢迎在评论指正。喜欢在微信看技术文章，想要获取更多的Java、BigData资源的同学，可以**关注微信公众号：MarkerJava**

> 另，如果觉得这本篇文章写得不错，有点东西的话，各位人才记得来个三连【点赞+关注+分享】。下一遍讲解ConcurrentHashMap集合，敬请期待哦。。。

