### ThreadLocal

### 1、ThreadLocal介绍

使用ThreadLocal其实就是避免两个线程之间的竞争。它是一种维护线程限制的更加规范的方式是使用ThreadLocal，它允许你将每个线程与持有数值的对象关联在一起。ThreadLocal为我们提供了get和set访问器。

### 2、线程本地（ThreadLocal）使用

线程本地，通常使用于防止在基于可变单体（Singleton）或者全局变量的设计中，出现共享。在多线程环境下，可以保证各个线程之间的变量互相隔离、相互独立。在线程中

假如你将要一个单线程的应用迁移到多线程环境中，这时候你就可以将共享的全局变量都转换为ThreadLocal类型了，这样就可以确保线程安全。但是前提是全局共享的语义允许这样。

### 3、ThreadLocal基本使用

```java
package com.lrm;


public class TestDemo {
    static class MyThread extends Thread {
        private static ThreadLocal<Integer> threadLocal = new ThreadLocal<>();
        @Override
        public void run() {
            super.run();
            for (int i = 0; i < 3; i++) {
                threadLocal.set(i);
                System.out.println(getName() + " threadLocal.get() = " + threadLocal.get());
            }
        }
    }
    public static void main(String[] args) {
        MyThread myThreadA = new MyThread();
        myThreadA.setName("ThreadA");

        MyThread myThreadB = new MyThread();
        myThreadB.setName("ThreadB");

        myThreadA.start();
        myThreadB.start();
    }
}

```

两个线程都在向threadLocal对象中set()数据值，但每个线程都还是能取出自己设置的数据，确实可以达到隔离线程变量的效果。

### 4、ThreadLocal源码解析

常用方法

* **get()方法**：获取与当前线程关联的ThreadLocal值。
* **set(T value)方法**：设置与当前线程关联的ThreadLocal值。
* **initialValue()方法**：设置与当前线程关联的ThreadLocal初始值。

get()方法