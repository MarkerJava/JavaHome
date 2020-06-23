**阅读导航**

* 一、LinkedList介绍

* 二、LinkedList重要常量

* 三、LinkedList源码剖析

### 一、LinkedList介绍

LinkedList是实现List接口，是由双向链表实现的，对于随机访问get和set，ArrayList优于LinkedList，因为LinkedList要移动指针。

新增和删除操作add和remove，LinedList比较占优势，因为ArrayList要移动数据。另外LinkedList实现了Deque接口，可以实现栈和队列的功能。

相对于ArrayList来说，最大区别在于底层数据结构不同，LinkedList的底层是一个双向链表，这也决定了它的最大优点，对于数据的修改比ArrayList更加方便快捷。

### 二、LinkedList重要常量

```java
transient int size = 0;//节点数量

//Pointer to first node.
transient Node<E> first;//第一个节点（头节点）


//Pointer to last node.
transient Node<E> last;//最后一个节点（尾节点）
```

### 三、LinkedList源码剖析

#### 1、add方法

````java
//add方法
//大家猜想一下，其实该方法就是把在链表最后添加元素的
public void add(int index, E element) {
        checkPositionIndex(index);
        if (index == size)
            linkLast(element);
        else
            linkBefore(element, node(index));
    }
//linkLast(element)实现的方法
void linkLast(E e) {
        final Node<E> l = last;
        final Node<E> newNode = new Node<>(l, e, null);
        last = newNode;
        if (l == null)
            first = newNode;
        else
            l.next = newNode;
        size++;
        modCount++;
    }
````

#### **2、get方法**

`````java
public E get(int index) {
        checkElementIndex(index);
        return node(index).item;
    }
//node(index).item
Node<E> node(int index) {
        // assert isElementIndex(index);
  			//下标长度的一半，从头遍历
        if (index < (size >> 1)) {
            Node<E> x = first;
            for (int i = 0; i < index; i++)
                x = x.next;
            return x;
        } else {//否则从尾部遍历
            Node<E> x = last;
            for (int i = size - 1; i > index; i--)
                x = x.prev;
            return x;
        }
    }
`````

#### **3、remove方法**

```java
public boolean remove(Object o) {
        if (o == null) {
            for (Node<E> x = first; x != null; x = x.next) {
                if (x.item == null) {
                    unlink(x);//删除
                    return true;
                }
            }
        } else {
            for (Node<E> x = first; x != null; x = x.next) {
              	//使用equals判断的
                if (o.equals(x.item)) {
                    unlink(x);
                    return true;
                }
            }
        }
        return false;
    }
```

**注**：LinkedList方法很多，基本都是可以通过源码慢慢看，我就不一一罗列了，有兴趣的可以自己通过一行一行看，其实源码都差不多的。