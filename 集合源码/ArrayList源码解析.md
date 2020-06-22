## ArrayList源码解析

#### 简介

ArrayList实现了List接口，继承了AbstractList，底层是数组实现的，一般我们把它认为是可以自增扩容的数组。它是非线程安全的。

#### 使用场景

一般多用于单线程环境下，它实现了Serializable接口，因此它支持序列化，能够通过序列化传输，但是实际上java类库中的大部分类都是实现了这个接口。实现了RandomAccess接口，支持快速随机访问，但是只是个标注接口而已，而没有实际的方法。底层是数组实现，所以直接用数组下标来索引，实现了Cloneable接口，能被克隆的。

ArrayList与Vector区别是？

Vector是线程安全的，所以ArrayList 性能相对Vector会好得多。

Vector之所以线程安全，原因是什么？

很简单，因为Vector几乎所有都被synchronize修饰，则线程安全。

1、构造函数

  空参构造方法

```java
//首先看两个重要的属性
//默认空容量，长度为0
private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};

//elementData是集合真正存储数据内容
transient Object[] elementData; 

//这是一个空的构造方法
public ArrayList() {
		//赋值
    this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
}
```

有参数构造方法【1】

```
//我们随便举个例子，定义有参如下
ArrayList<String> list = new ArrayList<>(8);
```

源码分析

```java
//我们传过来的参数8，讲传递给构造方法initialCapacity局部变量
public ArrayList(int initialCapacity) {
    if (initialCapacity > 0) {
    
    //如果大于0，创建一个数组，且是Object，长度是根据你创建数据指定的长度，
    //然后把地址 赋给ArrayList的成员变量elementData
        this.elementData = new Object[initialCapacity];
        
        //如果为0，则也是创建一个空的数组
    } else if (initialCapacity == 0) {
        this.elementData = EMPTY_ELEMENTDATA;
        
        //以上都不满足，就会抛出异常
    } else {
        throw new IllegalArgumentException("Illegal Capacity: "+
                                           initialCapacity);
    }
}
```

有参构造方法【2】

```java
public class Student {
    public static void main(String[] args) {
        ArrayList<String> list = new ArrayList<>();
        list.add("kobe");
        list.add("jm");
        ArrayList<String> list1 = new ArrayList<>(list);
        for (String lists : list) {
            System.out.println(lists);
        }
    }
}
```

进入ArrayList源码分析

```java
public ArrayList(Collection<? extends E> c) {
  //将构造方法中的参数转换成数组
    elementData = c.toArray();
  
    if ((size = elementData.length) != 0) {
      //再次进行判断
        if (elementData.getClass() != Object[].class)
          //数组的创建和拷贝
            elementData = Arrays.copyOf(elementData, size, Object[].class);
    } else {
        // 把空数组的地址赋值给集合存元素的数组
        this.elementData = EMPTY_ELEMENTDATA;
    }
}
```

#### toArray方法

```java
//集合转数组的方法
public Object[] toArray() {
////调用数组工具类的方法
    return Arrays.copyOf(elementData, size);
}
```

#### copyOf方法

```java
//elementData复制给original，size复制给newLength
public static <T> T[] copyOf(T[] original, int newLength) {
//再次调用方法得到一个数组
    return (T[]) copyOf(original, newLength, original.getClass());
}

public static <T,U> T[] copyOf(U[] original, int newLength, Class<? extends T[]> newType) {
        @SuppressWarnings("unchecked")
        //做了个三元运算，不管如何，都会创建一个新数组
        //新的数组长度是和集合的size一致的
        T[] copy = ((Object)newType == (Object)Object[].class)
            ? (T[]) new Object[newLength]
            : (T[]) Array.newInstance(newType.getComponentType(), newLength);
        //数组拷贝
        System.arraycopy(original, 0, copy, 0,
                         Math.min(original.length, newLength));
        //返回新数组
        return copy;
    }
```

add方法（将指定元素追加到列表末尾）

```java
private int newCapacity(int minCapacity) {
    // >>表示右移，右移几位就相当于除以2的几次幂
    // <<表示左移，左移几位就相当于除以2的几次幂
    int oldCapacity = elementData.length;
  //扩容核心算法，原容量的1.5陪
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    if (newCapacity - minCapacity <= 0) {
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA)
            return Math.max(DEFAULT_CAPACITY, minCapacity);
        if (minCapacity < 0) // overflow
            throw new OutOfMemoryError();
        return minCapacity;
    }
    return (newCapacity - MAX_ARRAY_SIZE <= 0)
        ? newCapacity
        : hugeCapacity(minCapacity);
}
```

