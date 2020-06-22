*前面，几篇文章我们讲了synchronized、volatile和CAS，今天我们一起来聊聊Atomic到底是个什么？*

### Atomic介绍

例如，之前我们写递增m++时，在多线程访问的情况下得加锁。那现在我们不加锁而是用AtomicInteger，它内部就已经帮我们实现了原子操作，直接写 count.incrementAndGet(); //count1++ 这个就相当于count++。原来我们对count是需要加锁的，而现在就不需要加锁。

注：并发包java.util.concurrent的原子类都是存放在java.util.concurren.atomic下的。

### Atomic的4中原子类

基本类型
●AtomicInteger: 整形原子类
●AtomicLong: 长整型原子类
●AtomicBoolean: 布尔型原子类

数组类型
●AtomicIntegerArray: 整形数组原子类
●AtomicLongArray: 长整形数组原子类
●AtomicReferenceArray: 引用类型数组原子类

引用类型
●AtomicReference: 引用类型原子类
●AtomicStampedReference: 原子更新带有版本号的引用类型
●AtomicMarkableReference :原子更新带有标记位的引用类型

对象的属性修改类型
●AtomicIntegerFieldUpdater: 原子更新整形字段的更新器
●AtomicLongFieldUpdater: 原子更新长整形字段的更新器

### AtomicInteger使用

常用方法

```java
//获取当前的值
public final int get() {return value;}
public final void set(int newValue) {value = newValue;}
//获取当前的值，并设置新的值
public final int getAndSet(int newValue) {return U.getAndSetInt(this, VALUE, newValue);}
//获取当前的值，并且自增
public final int getAndIncrement() {return U.getAndAddInt(this, VALUE, 1);}
//获取当前的值，并且自减
public final int getAndDecrement() {return U.getAndAddInt(this, VALUE, -1);}
//如果输入的数值等于预期值，则以原子方式将该值设置为输入值
public final boolean compareAndSet(int expectedValue, int newValue) {
        return U.compareAndSetInt(this, VALUE, expectedValue, newValue);}
//获取当前值，并且加上预期值
public final int getAndAdd(int delta) {return U.getAndAddInt(this, VALUE, delta);}
//最终设置为newValue，使得让其他线程一段时间后还是可以读取到旧值
public final void lazySet(int newValue) {U.putIntRelease(this, VALUE, newValue);}
```

### AtomicInteger使用例子