

> 水平有限，难免会有疏漏之处，如有错误，还请指出，感谢！

### HashMap的解析有这么几个步骤：

1. hash 算法。
2. 初始化数组。
3. 通过 hash 计算下标并检查 hash 是否冲突，也就是对应的下标是否已存在元素。
4. 通过判断是否含有元素，决定是否创建还是追加链表或树。
5. 判断已有元素的类型，决定是追加树还是追加链表。
6. 判断是否超过阀值，如果超过，则重新散列数组。
7. Java 8 重新散列时是如何优化的。

#### hash算法

```java
// 存储时:
// 这个hashCode方法,理解每个key的hash是一个固定的int值即可
int hash = key.hashCode(); 
int index = hash % Entry[].length;
Entry[index] = value;

// 取值时:
int hash = key.hashCode();
int index = hash % Entry[].length;
return Entry[index];
```

### 初始化数组

#### 通过 hash 计算下标并检查 hash 是否冲突，也就是对应的下标是否已存在元素。

put方法

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