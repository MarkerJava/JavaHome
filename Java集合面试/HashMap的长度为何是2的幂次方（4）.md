### 为什么要重写 hashcode 和 equals 方法？

hashcode方法，会返回一个哈希值，哈希值对数组的长度取余后会确定一个存储的下标位置。

不同的哈希值取余之后的结果可能是相同的摸着时候就用equals方法判断是否为相同的对象，不同则在链表中插入。

### 为什么在重写equals方法的时候要重写hashcode的方法？

如果只重写了equals方法，而不重写hashcode的方法，会造成hashcode的值不同，而equals()方法判断出来的结果为true。

1. 不被重写（原生）的hashCode值是根据内存地址换算出来的一个值。
2.    不被重写（原生）的equals方法是严格判断一个对象是否相等的方法（object1 == object2）。

> 当我们用HashMap存入自定义的类时，如果不重写这个自定义类的equals和hashCode方法，得到的结果会和我们预期的不一样。我们来看WithoutHashCode.java这个例子。

为什么在重写equals方法的时候要重写hashcode的方法？

为了提高效率，比如现在有个集合有10万个元素，需要从中找出包含某个对象，如果只写equals方法的话，那只能取出每个元素一一进行equals直到相就返回true，否则 false。但假如我们要找的元素刚好在第10万个位置，这样比较效率势必下降很快。

这时候，哈希算法来提高从该集合中查找元素的效率，这种方式将集合分成若干个存储区域（可以看成一个个桶），每个对象可以计算出一个哈希码，可以根据哈希码分组，每组分别对应某个存储区域，这样一个对象根据它的哈希码就可以分到不同的存储区域（不同的桶中）

一个对象一般有key和value，可以根据key来计算它的hashCode。假设现在全部的对象都已经根据自己的hashCode值存储在不同的存储区域中了，那么现在查找某个对象（根据对象的key来查找），不需要遍历整个集合了，现在只需要计算要查找对象的key的hashCode，然后找到该hashCode对应的存储区域，在该存储区域中来查找就可以了，这样效率也就提升了很多。说了这么多相信你对hashCode的作用有了一定的了解，下面就来看看hashCode和equals的区别和联系。
