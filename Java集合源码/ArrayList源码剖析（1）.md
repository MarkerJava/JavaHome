## ArrayList源码解析

**阅读导航**

- 一、ArrayList简介
- 二、ArrayList和LinkedList区别
- 三、ArrayList与Vector区别是？
- 四、ArrayList源码分析
- 五、ArrayList遍历方式



#### 一、ArrayList简介简介

ArrayList实现了List接口，继承了AbstractList。底层基于动态数组实现的，对于随机访问非常快，它是通过index直接定位到数组对应位置的节点，所以随机访问占有优势。（非线程安全）



### 二、ArrayList 和 LinkedList 的区别

1. ArrayList底层基于动态数组实现；而LinkedList底层基于链表实现
2. ArrayList随机访问快，主要是通过index直接定位到数组对应位置的节点；而LinkedList需要从头结点或尾节点开始遍历，直到寻找到目标节点，因此在效率上ArrayList优于LinkedList
3. ArrayList对于插入和删除效率低，因为需要移动目标节点后面的节点（通过System.arraycopy方法移动节点）；而LinkedList只需修改目标节点前后节点的next或prev属性即可，所以效率也会高。



### 三、ArrayList与Vector区别是？

Vector是线程安全的，几乎所有方法都被synchronize修饰，则线程安全。所以ArrayList 性能相对Vector会好得多。



### 四、ArrayList源码分析

#### 1、重要常量

```java
//默认的初始容量为10
private static final int DEFAULT_CAPACITY = 10;

private static final Object[] EMPTY_ELEMENTDATA = {};
//默认空容量，长度为0
private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
//elementData是集合真正存储数据内容
transient Object[] elementData;
// ArrayList中实际数据的数量
private int size;
```

#### 2、无参构造函数

```java
//默认容量为10
public ArrayList() {
        this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
    }
```

#### 3、有参构造函数

```java
//带初始容量大小的构造函数
public ArrayList(int initialCapacity) {
        if (initialCapacity > 0) {//初始容量大于0,则实例化数组
            this.elementData = new Object[initialCapacity];
        } else if (initialCapacity == 0) {//初始容量或初始化等于0，就创建一个空的数组
            this.elementData = EMPTY_ELEMENTDATA;
        } else {//否则初始容量小于0，就抛异常
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        }
    }
```

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

#### 4、toArray方法

```java
//集合转数组的方法
public Object[] toArray() {
////调用数组工具类的方法
    return Arrays.copyOf(elementData, size);
}
```

#### 5、copyOf方法

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

#### 6、add方法（将指定元素追加到列表末尾）

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

##### add方法（重点）

具体实现步骤分为：

* 首先判断是否需要扩容
* 再插入元素

##### 1.3 当容量长度小于默认容量的长度10时，源码解析（5❤️）

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

##### 1.4 当容量长度大于默认容量的长度10时，源码解析（5❤️）

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

**总结**：添加（add）方法的实现，添加时首先检查数组的容量是否满足

* ArrayList首次扩容，容量为10，不满足则扩容到到原来的1.5倍
* 第一次扩容后，容量还是小于minCapacity（就是我们添加的容量如15，则minCapacity=15），就将容量扩容为minCapacity
* 如果容量足够就直接添加，不够就需要进行扩容
* 扩容过程的空间复杂度是O(2n)，因为需要拷贝一个次旧的数组
* ArrayList是**基于动态数组实现的**，在增删时候，需要数组的拷贝复制

##### 1.5 get、set、remove方法

````java
//get方法
public E get(int index) {
  			//检查角标
        Objects.checkIndex(index, size);
        return elementData(index);//返回
    }
//set方法
public E set(int index, E element) {
        Objects.checkIndex(index, size);
  			//将进行替换，返回旧值
        E oldValue = elementData(index);
        elementData[index] = element;
        return oldValue;
    }
//remove方法实现步骤：
//1.检查角标
//2.删除元素
//3.计算出需要移动个数
//设置null，GC回收
//每次删除一个数，后边的元素会往前移动
public E remove(int index) {
        Objects.checkIndex(index, size);
        final Object[] es = elementData;

        @SuppressWarnings("unchecked") E oldValue = (E) es[index];
        fastRemove(es, index);

        return oldValue;
    }
````

**使用remove的应用Iterator遍历删除是最安全的**如下：

````java
public void testRemove(){
    List<String> strings = new ArrayList<>();
    strings.add("1");
    strings.add("2");
    strings.add("3");
    strings.add("4");
    Iterator<String> iterator = strings.iterator();
    while (iterator.hasNext()){
        String next = iterator.next();
        iterator.remove();
    }
 
    System.out.println(strings);
}
````

ArrayList总结：

* ArrayList的默认初始化容量是10，每次扩容时候增加原先容量的一半，也就是变为原来的1.5倍
* 删除元素时不会减少容量大小，需要减少容量，就调用**trimToSize()**
* 底层数组结构，线程不安全
* 动态数组实现，增删时候，需要数组的拷贝复制
* 第一次扩容后，如果容量还是小于minCapacity，就将容量扩充为minCapacity。

### 五、ArrayList遍历方式

1. 通过迭代器遍历

```java
 Iterator iter = list.iterator();
    while (iter.hasNext()) {
        System.out.println(iter.next());
    }
```

2. for循环遍历

```java
for(String str:list){
    System.out.println(str);
　　 }  
```

