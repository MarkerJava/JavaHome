# 第一章	ArrayList

## 1、核心属性源码：

```java
//ArrayList初始容量为10
private static final int DEFAULT_CAPACITY = 10;
//用于空实例的共享空数组实例
private static final Object[] EMPTY_ELEMENTDATA = {};
//用于默认大小空实例的共享空数组实例。
//我们把它从EMPTY_ELEMENTDATA数组中区分出来，以知道在添加第一个元素时容量需要增加多少
private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
//保存ArrayList数据的数组
transient Object[] elementData; 
//该属性设置容量大小，ArrayList 所包含的元素个数
private int size;
```

## 2、构造方法(验证核心属性)

```java
//1、构造具有指定初始容量的空列表
public ArrayList(int initialCapacity) {
  			//如果指定容量大于0，那么数组就进行初始化成对应的容量
        if (initialCapacity > 0) {
            this.elementData = new Object[initialCapacity];
        } else if (initialCapacity == 0) {
            //如果初始容量为0，它会默认给定一个空的数组（EMPTY_ELEMENTDATA）
            this.elementData = EMPTY_ELEMENTDATA;
        } else {
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        }
    }
//2、构造一个初始容量为10的空列表。
public ArrayList() {
        this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
    }
//3、按照集合的迭代器返回的顺序构造一个包含指定集合元素的列表。
public ArrayList(Collection<? extends E> c) {
        elementData = c.toArray();
        if ((size = elementData.length) != 0) {
            // defend against c.toArray (incorrectly) not returning Object[]
            // (see e.g. https://bugs.openjdk.java.net/browse/JDK-6260652)
            if (elementData.getClass() != Object[].class)
                elementData = Arrays.copyOf(elementData, size, Object[].class);
        } else {
            // replace with empty array.
            this.elementData = EMPTY_ELEMENTDATA;
        }
    }
```

## 3、ArrayList的扩容机制

```java
//在使用ensureCapacity操作添加大量元素之前，应用程序可以增加ArrayList实例的容量。
//这可能会减少增量重新分配的数量。
public void ensureCapacity(int minCapacity) {
        if (minCapacity > elementData.length
            && !(elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA
                 && minCapacity <= DEFAULT_CAPACITY)) {
            modCount++;
            grow(minCapacity);
        }
    }
```

## 4、ArrayList扩容的核心方法。

```java
//oldCapacity旧容量，newCapacity新容量
private int newCapacity(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
  			//将新容量更新为旧容量的1.5倍
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

    private static int hugeCapacity(int minCapacity) {
        if (minCapacity < 0) // overflow
            throw new OutOfMemoryError();
        return (minCapacity > MAX_ARRAY_SIZE)
            ? Integer.MAX_VALUE
            : MAX_ARRAY_SIZE;
    }
//返回此列表中的元素数。
public int size() { return size; }
```

## 5、add方法（重点）

>具体实现步骤分为：首先判断是否需要扩容、再插入元素

##### 当添加元素时容量长度小于默认容量的长度10时，源码解析

````java
//1、添加元素，e获取你添加的元素
public boolean add(E e) {
        modCount++;//在之前的元素自增
        add(e, elementData, size);
        return true;
    }
//2、判断是否需要扩容
private void add(E e, Object[] elementData, int s) {
  			//判断s长度是否等于默认容量长度10
  			//如果大于默认容量10，则走扩容elementData = grow();
  			//如果小于默认容量10，则走直接添加elementData[s] = e;
        if (s == elementData.length)
            elementData = grow();
        elementData[s] = e;
  			//小于默认容量长度10，则size等于原来的+1
        size = s + 1;
    }

//3、最终返回size = s + 1;
public boolean add(E e) {
        modCount++;
        add(e, elementData, size);
  			//最终的返回
        return true;
    }

````

##### 当容量长度大于默认容量的长度10时，源码解析

````java
//1、添加元素
public boolean add(E e) {
        modCount++;
        add(e, elementData, size);
        return true;
    }
//2、判断是否需要扩容
private void add(E e, Object[] elementData, int s) {
  			//判断s长度是否等于默认容量长度10
  			//如果大于默认容量10，则走扩容elementData = grow();
  			//如果小于默认容量10，则走直接添加elementData[s] = e;
        if (s == elementData.length)
            elementData = grow();
        elementData[s] = e;
        size = s + 1;
    }
//3、默认容量+1
private Object[] grow() {
        return grow(size + 1);
    }
//4、minCapacity=默认容量size+1
private Object[] grow(int minCapacity) {
  			//要复制的数组；拷贝的新数组长度
        return elementData = Arrays.copyOf(elementData,
                                         newCapacity(minCapacity));//进行扩容了
    }
//5、实现扩容
//用来确定是否需要容量，最后调用grow()来扩容
//假设当添加11个元素时，minCapacity=11，而默认数组为10，此时需要进行扩容
private int newCapacity(int minCapacity) {
        // overflow-conscious code
  			//5.1 旧容量10
  			//扩容1.5倍
        int oldCapacity = elementData.length;
  			//5.2新的容量=旧容量+(oldCapacity >> 1)
        int newCapacity = oldCapacity + (oldCapacity >> 1);
  			//5.3判断，如果新的容量-最小容量<=0(也就是扩容后的容量-minCapacity)
  			//minCapacity(最小容量=默认的容量10+你存入元素得到的长度)
        if (newCapacity - minCapacity <= 0) {
            if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA)
                return Math.max(DEFAULT_CAPACITY, minCapacity);
            if (minCapacity < 0) // overflow
                throw new OutOfMemoryError();
            return minCapacity;
        }
  			//5.4新的容量减去MAX_ARRAY_SIZE <= 0的话，就等于扩容后的长度
        return (newCapacity - MAX_ARRAY_SIZE <= 0)
            ? newCapacity
            : hugeCapacity(minCapacity);
    }
//6、得到最小容量（就是假如你存入长度为11，则最终minCapacity=11的意思）
private Object[] grow(int minCapacity) {
        return elementData = Arrays.copyOf(elementData,
                                           newCapacity(minCapacity));
    }
		//7、newLength扩容后的长度、original原始的
    @SuppressWarnings("unchecked")
    public static <T> T[] copyOf(T[] original, int newLength) {
        return (T[]) copyOf(original, newLength, original.getClass());
    }
		//8、数组的拷贝复制
    public static <T,U> T[] copyOf(U[] original, int newLength, Class<? extends T[]> newType) {
        @SuppressWarnings("unchecked")
        T[] copy = ((Object)newType == (Object)Object[].class)
            ? (T[]) new Object[newLength]
            : (T[]) Array.newInstance(newType.getComponentType(), newLength);
      	//Math.min进行计算
        System.arraycopy(original, 0, copy, 0,
                         Math.min(original.length, newLength));
        return copy;
    }
		//9、a=默认容量10，b=扩容后的容量
    public static int min(int a, int b) {
        return (a <= b) ? a : b;
    }
//10、再回到System.arraycopy(original, 0, copy, 0,
public static <T,U> T[] copyOf(U[] original, int newLength, Class<? extends T[]> newType) {
        @SuppressWarnings("unchecked")
        T[] copy = ((Object)newType == (Object)Object[].class)
            ? (T[]) new Object[newLength]
            : (T[]) Array.newInstance(newType.getComponentType(), newLength);
  			//复制
        System.arraycopy(original, 0, copy, 0,
                         Math.min(original.length, newLength));
  			//最终返回复制所得到的
        return copy;
    }
//11、复制结束return (T[]) copyOf(original, newLength,original.getClass());
public static <T> T[] copyOf(T[] original, int newLength) {
        return (T[]) copyOf(original, newLength, original.getClass());
    }
//12、grow()方法扩容得到最终的长度
private Object[] grow() {
        return grow(size + 1);
    }
//13、
private void add(E e, Object[] elementData, int s) {
        if (s == elementData.length)
          	//13.1	elementData = grow();
            elementData = grow();
  			//13.2	e是存入的元素
        elementData[s] = e;
  			//13.3最终长度为size = s + 1;
        size = s + 1;
    }
//14、结束
public boolean add(E e) {
        modCount++;
        add(e, elementData, size);
  			//返回
        return true;
    }
````

**小结**：

```reStructuredText
1、添加元素时，首先进行判断是否大于默认容量10
2、如果，小于默认容量，直接在原来基础上+1，元素添加完毕
3、如果，大于默认容量，则需要进行扩容，扩容核心是grow()方法
   3.1 扩容之前，首先创建一个新的数组，旧数组被复制到新的数组中
       这样就得到了一个全新的副本，我们在操作时就不会影响原来数组了
   3.2 然后通过位运算符将新的容量更新为旧容量的1.5陪（原来长度的一半再加上原长度也就是每次扩容是原来的1.5倍）
   3.3 如果新的容量-旧的容量<=0，就拿新的容量-最大容量长度如果<=0的，那么最终容量就是扩容后的容量
```

**总结**：添加（add）方法的实现，添加时首先检查数组的容量是否满足

* ArrayList首次扩容，容量为10，不满足则扩容到到原来的1.5倍
* 第一次扩容后，容量还是小于minCapacity（就是我们添加的容量如15，则minCapacity=15），就将容量扩容为minCapacity
* 如果容量足够就直接添加，不够就需要进行扩容
* 扩容过程的空间复杂度是O(2n)，因为需要拷贝一个次旧的数组
* ArrayList是**基于动态数组实现的**，在增删时候，需要数组的拷贝复制

