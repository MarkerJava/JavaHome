### HashMap底层原理，你真的懂了吗？

1、HashMap介绍

本文使用JDK1.8，之前版本只会做简单介绍，具体有哪些变化即可。

JDK1.8之前的版本HashMap底层实现是**数组+链表**的，也称之“散列表”。JDK1.8之后，HashMap底层实现已经不止是**数组+链表**了，而是**数组+链表+红黑树**实现。红黑树的目的是什么，就是让链表插入和查询效率提升。

2、如何去看懂HashMap底层原理

步骤1：首先我们以put()方法作为源码入口

```java
public V put(K key, V value) {
        return putVal(hash(key), key, value, false, true);
    }
```

该方法具体实现是putVal这个方法，执行这个方法之前先进行计算hash值，源码如下

```java
static final int hash(Object key) {
   int h;
  //^:按位异或
  // >>>：无符号右移，忽略符号位，空位都以0补齐
  return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
    }
```

步骤2： putVal方法，也是最重要的方法之一，需要重点理解源码如下

```java
//该方法就是传进来一个hash，key,value
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
        Node<K,V>[] tab; Node<K,V> p; int n, i;
  			//1、首先判断数组为null或数组为0时，就会调用resize方法初始化（resize包括对数组扩容及初始化）
        if ((tab = table) == null || (n = tab.length) == 0)
            n = (tab = resize()).length;//n就是初始化后数组大小
  			//2、再判断，根据计算出hash值对应的数组下标是什么，如果数组下标为null，则newNode
        if ((p = tab[i = (n - 1) & hash]) == null)
            tab[i] = newNode(hash, key, value, null);
  			//2、再判断，根据计算出hash值对应的数组下标是什么，如果数组下标不为null，则进行下面操作
        else {
            Node<K,V> e; K k;
          	//首先也是判断K是否相等，相等的话就直接覆盖value值
          	//判断K不相等，那么就会有多种情况可能红黑树TreeNode或链表for其实就是遍历链表
            if (p.hash == hash &&
                ((k = p.key) == key || (key != null && key.equals(k))))
                e = p;
          	//红黑树
            else if (p instanceof TreeNode)
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            else {
                for (int binCount = 0; ; ++binCount) {
                    if ((e = p.next) == null) {//链表
                      	//尾插法，JDK1.7时是头插法
                        p.next = newNode(hash, key, value, null);
                      	//判断当前链表的长度是否大于等于默认值-1，就是数组大小等于8或者长度等于7
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                            treeifyBin(tab, hash);//当前数组tab和传入key的hash值
                        break;
                    }
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        break;
                    p = e;
                }
            }
            if (e != null) { // existing mapping for key
                V oldValue = e.value;
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value;
                afterNodeAccess(e);
                return oldValue;
            }
        }
        ++modCount;
        if (++size > threshold)
            resize();
        afterNodeInsertion(evict);
        return null;
    }
```

从上面代码可知，如果数组大小大于等于8时就进行塑化“binCount >= TREEIFY_THRESHOLD - 1”

步骤3：如何进行塑化的呢

```java
/**
 * Replaces all linked nodes in bin at index for given hash unless
 * table is too small, in which case resizes instead.
 */
final void treeifyBin(Node<K,V>[] tab, int hash) {
    int n, index; Node<K,V> e;
    if (tab == null || (n = tab.length) < MIN_TREEIFY_CAPACITY)
        resize();
    else if ((e = tab[index = (n - 1) & hash]) != null) {
        TreeNode<K,V> hd = null, tl = null;
        do {
            TreeNode<K,V> p = replacementTreeNode(e, null);
            if (tl == null)
                hd = p;
            else {
                p.prev = tl;
                tl.next = p;
            }
            tl = p;
        } while ((e = e.next) != null);
        if ((tab[index] = hd) != null)
            hd.treeify(tab);
    }
```

链表转红黑树小结：

1. 首先它会判断，如果你的数组为null或数组长度小于64，则进行扩容，而不是转红黑树。
2. 如果数组长度大于等于64，则进行红黑树。

### TreeNode类

TreeNode它是红黑树的一个节点，同时也是实现双向链表，了解几个属性

```java
static final class TreeNode<K,V> extends LinkedHashMap.Entry<K,V> {
        TreeNode<K,V> parent;  // red-black tree links
        TreeNode<K,V> left;
        TreeNode<K,V> right;
        TreeNode<K,V> prev;    // needed to unlink next upon deletion
        boolean red;
        TreeNode(int hash, K key, V val, Node<K,V> next) {
            super(hash, key, val, next);
        }
```

