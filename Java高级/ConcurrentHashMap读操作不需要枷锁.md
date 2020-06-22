### 你真的知道，ConcurrentHashMap读操作为什么不需要加锁？

> 看过源码都知道，除非你没有用过JDK1.8，那是不可能的嘛。

### 介绍

之前看过JDK1.7源码时，ConcurrentHashMap它是采用Segment + HashEntry + ReentrantLock的方式进行实现的，然而JDK1.8中放弃了Segment臃肿的设计，取而代之的是采用Node + CAS + Synchronized来保证并发安全进行实现。

### CAS是什么

CAS可能有的同学不知道，那我们科普一下CAS到底是什么？之前CAS我有过单独一篇来讲解的，在这里我们就简单了解即可，需要了解更多可以看我之前的文章。

CAS在JDK新增的锁里头，CAS是被大量的运用，大家有时间可以跟踪源码看看。CAS全称叫compare and swap（比较或者交换），<font color="red">**就是它在没有锁的状态下，可以保证多个线程对一个值的更新。**</font>（但是使用CAS会造成ABA问题）

### ConcurrentHashMap的get操作源码

ConcurrentHashmap并发集合框架是线程安全，接下来看get操作时源码，会发现get操作全程是没有加任何锁的。

```java
//会发现源码中没有一处加了锁
public V get(Object key) {
    Node<K,V>[] tab; Node<K,V> e, p; int n, eh; K ek;
    int h = spread(key.hashCode()); //计算hash
    if ((tab = table) != null && (n = tab.length) > 0 &&
        (e = tabAt(tab, (n - 1) & h)) != null) {//读取首节点的Node元素
        if ((eh = e.hash) == h) { //如果该节点就是首节点就返回
            if ((ek = e.key) == key || (ek != null && key.equals(ek)))
                return e.val;
        }
        //hash值为负值表示正在扩容，这个时候查的是ForwardingNode的find方法来定位到nextTable来
        //eh=-1，说明该节点是一个ForwardingNode，正在迁移，此时调用ForwardingNode的find方法去nextTable里找。
        //eh=-2，说明该节点是一个TreeBin，此时调用TreeBin的find方法遍历红黑树，由于红黑树有可能正在旋转变色，所以find里会有读写锁。
        //eh>=0，说明该节点下挂的是一个链表，直接遍历该链表即可。
        else if (eh < 0)
            return (p = e.find(h, key)) != null ? p.val : null;
        while ((e = e.next) != null) {//既不是首节点也不是ForwardingNode，那就往下遍历
            if (e.hash == h &&
                ((ek = e.key) == key || (ek != null && key.equals(ek))))
                return e.val;
        }
    }
    return null;
}
```

### 如何读到的数据不是脏数据

很多会疑惑，为什么get没有加锁，而ConcurrentHashMap又是如何保证读到的数据不是脏数据的呢？这时候就要用到Volatile了。什么又是Volatile感觉又太陌生了，不要急，今天我都给大家介绍。

### 什么是Volatile

关于Volatile，我前两篇文章也是单独讲解，所以需要了解更深入的可以找找我之前的文章，今天你知道Volatile是什么即可。

volatile是Java提供的一种轻量级的同步机制，能够**保证线程可见性，但不保证原子性**。意思就是当给变量值加volatile之后，就能够保证当一个线程改变这个变量时，另一个线程马上就能看到。

### 总结

* get操作全程不需要加锁是因为Node的成员val是用volatile修饰的和数组用volatile修饰没有关系。
* 数组用volatile修饰主要是保证在数组扩容的时候保证可见性。
* JDK1.8是用红黑树来优化链表，因为如果链表很长需要遍历是一个很漫长的过程，然而红黑树的遍历效率是很快。
* JDK1.8降低锁的粒度，知道JDK1.7版本锁的粒度是基于Segment的，包含多个HashEntry所以效率会低。

