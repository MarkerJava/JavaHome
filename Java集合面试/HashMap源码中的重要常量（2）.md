想要看懂HashMap源码，必须知道的12个重要常量

### HashMap的重要常量

```
//HashMap的默认容量为16
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; 

//HashMap的最大支持容量，2的30次方，大概就是10多亿
static final int MAXIMUM_CAPACITY = 1 << 30;

//HashMap的默认加载因子
static final float DEFAULT_LOAD_FACTOR = 0.75f;

//当数组大于默认8-1，转化为链表
static final int TREEIFY_THRESHOLD = 8;

//Bucket中红黑树存储的Node小于该默认值，转化为链表
static final int UNTREEIFY_THRESHOLD = 6;

//数组长度大于等于64，则进行红黑树
static final int MIN_TREEIFY_CAPACITY = 64;

//存储元素的数组，总是2的n次幂
transient Node<K,V>[] table;

//存储具体元素的集
transient Set<Map.Entry<K,V>> entrySet;

//HashMap中存储的键值对的数量
transient int size;

//HashMap扩容和结构改变的次数。
transient int modCount;

//扩容的临界值，=容量*填充因子
int threshold;

final float loadFactor;//填充因子
```

### 聊聊HashMap什么时候进行扩容

HashMap如时扩容，当向容器添加元素时，如果元素个数大于等于阈值（数组的长度乘以加载因子值的时），loadFactor 的默认值就是 DEFAULT_LOAD_FACTOR=0.75，就要进行扩容。

也就是说，HashMap默认情况下容量是16（DEFAULT_INITIAL_CAPACITY = 1 << 4; ），当添加元素超过这个默认容量时。那么当HashMap中元素个数超过16*0.75=12时，就把数组的大小扩展为2乘16=32，然后再重新计算每个元素在数组中的位置。

注：这是一个非常消耗性能的操作的，扩容时性能也会受到影响的。

### HashMap什么时候进行树形化

进行树形化有两个条件，首先当HashMap中一个链的对象个数如果达到8个，且数组长度大于等于64，则进行红黑树，结点类型由Node变成TreeNode类型的。但是当映射关系被移除后， 下次resize方法时判断树的结点个数低于6个，也会把树再转为链表的。

如意HashMap中一个链的对象个数如果达到8个，但如果capacity没有达到64，那么HashMap会先扩容解决。

### 负载因子值的大小，对HashMap有什么影响

该问题如何回答如下：

* 首先知道负载因子的大小是主要就是决定了HashMap的数据密度的嘛
* 如果负载因子越大密度越大，就会可能发生碰撞的几率越高，数组中的链表也会越容易长, 这样的话造成查询和插入时的比较次数增多，性能会下降。（可能又引出一个面试题：如何解决Hash碰撞问题？）
* 如果负载因子越小，就会越容易触发扩容，数据密度也越小，发生碰撞几率就小，数组中链表越短对于查询和插入时比较次数也会少一些，性能也会提高。
* 但是有一个缺点，负载因子越小，就会越容易触发扩容嘛，扩容会影响性能，所以建议初始化预设给它大一点空间。

### 那你觉得负载因子设置为多少较为合理

我会考虑将负载因子设置为0.7~0.75，因为此时平均检索长度接近于常数了，这样会更好一些。

其他面试题：什么是负载因子？什么是填充比？什么是吞吐临界值？

