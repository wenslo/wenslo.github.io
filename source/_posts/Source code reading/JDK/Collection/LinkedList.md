---
title: LinkedList源码

categories: 

- 源码阅读

tags: 

- JDK源码
date: 2019-04-27
---

> LinkedList是基于链表，是Java中线性表的链表实现。

```java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable{}
public class LinkedList<E>
    extends AbstractSequentialList<E>
    implements List<E>, Deque<E>, Cloneable, java.io.Serializable{}
```

解读：

* LinkedList实现了Deque接口，而Deque又继承自Queue，说明Linked是支持双/单向队列相关操作。

问题：

* ArrayList继承于AbstractList，而LinkedList继承于AbstractSequentialList，这两个类区别在哪？(**问题1**)
* ArrayList实现了RandomAccess接口，这个接口作用是什么？为什么实现？LinkedList为什么没有实现？(**问题2**)

```java
    /**
     * Pointer to first node.
     */
    transient Node<E> first;

    /**
     * Pointer to last node.
     */
    transient Node<E> last;
private static class Node<E> {
    E item;
    Node<E> next;
    Node<E> prev;

    Node(Node<E> prev, E element, Node<E> next) {
      this.item = element;
      this.next = next;
      this.prev = prev;
    }
}
```

解读：

* 由上可知，Node不仅指向了它的下一个节点，也有其上一个节点的指针。由此说明，LinkedList是双向循环链表，而非单向。
* 由于有first和last的引用，所以，使用queue相关操作会很方便。

```java
    /**
     * Appends the specified element to the end of this list.
     *
     * <p>This method is equivalent to {@link #addLast}.
     *
     * @param e element to be appended to this list
     * @return {@code true} (as specified by {@link Collection#add})
     */
    public boolean add(E e) {
        linkLast(e);
        return true;
    }
    /**
     * Inserts the specified element at the specified position in this list.
     * Shifts the element currently at that position (if any) and any
     * subsequent elements to the right (adds one to their indices).
     *
     * @param index index at which the specified element is to be inserted
     * @param element element to be inserted
     * @throws IndexOutOfBoundsException {@inheritDoc}
     */
    public void add(int index, E element) {
        checkPositionIndex(index);

        if (index == size)
            linkLast(element);
        else
            linkBefore(element, node(index));
    }
    /**
     * Links e as last element.
     */
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
    /**
     * Inserts element e before non-null Node succ.
     */
    void linkBefore(E e, Node<E> succ) {
        // assert succ != null;
        final Node<E> pred = succ.prev;
        final Node<E> newNode = new Node<>(pred, e, succ);
        succ.prev = newNode;
        if (pred == null)
            first = newNode;
        else
            pred.next = newNode;
        size++;
        modCount++;
    }
    /**
     * Returns the (non-null) Node at the specified element index.
     */
    Node<E> node(int index) {
        // assert isElementIndex(index);

        if (index < (size >> 1)) {
            Node<E> x = first;
            for (int i = 0; i < index; i++)
                x = x.next;
            return x;
        } else {
            Node<E> x = last;
            for (int i = size - 1; i > index; i--)
                x = x.prev;
            return x;
        }
    }
```

解读：

* 由于是链表，没有ArrayList的数组扩容和数组移动相关操作。数据多的情况下，add的性能优于ArrayList
* 指定位置add的话，如果是非最后一个位置添加元素，则需要根据下标先找到原先元素，在其之前进行操作。真是想方设法优化速度。。。
* node()并不是完全从头开始遍历获取。而是根据index是否小于size的一半，来决前序遍历还是后序遍历。这里应该还能继续优化一次，用二分的话，一次次缩小区间，这样岂不是更小了。

```java
    /**
     * Returns the element at the specified position in this list.
     *
     * @param index index of the element to return
     * @return the element at the specified position in this list
     * @throws IndexOutOfBoundsException {@inheritDoc}
     */
    public E get(int index) {
        checkElementIndex(index);
      //node方法在上面有
        return node(index).item;
    }

    /**
     * Replaces the element at the specified position in this list with the
     * specified element.
     *
     * @param index index of the element to replace
     * @param element element to be stored at the specified position
     * @return the element previously at the specified position
     * @throws IndexOutOfBoundsException {@inheritDoc}
     */
    public E set(int index, E element) {
        checkElementIndex(index);
        Node<E> x = node(index);
        E oldVal = x.item;
        x.item = element;
        return oldVal;
    }
```

解读：

* get需要循环遍历list的1/2。性能肯定比不上数组（ArrayList）的直接获取。
* set方法并没有增加modCount。那么，循环中可以放肆的set….

```java
    public boolean remove(Object o) {
        if (o == null) {
            for (Node<E> x = first; x != null; x = x.next) {
                if (x.item == null) {
                    unlink(x);
                    return true;
                }
            }
        } else {
            for (Node<E> x = first; x != null; x = x.next) {
                if (o.equals(x.item)) {
                    unlink(x);
                    return true;
                }
            }
        }
        return false;
    }
    public int lastIndexOf(Object o) {
        int index = size;
        if (o == null) {
            for (Node<E> x = last; x != null; x = x.prev) {
                index--;
                if (x.item == null)
                    return index;
            }
        } else {
            for (Node<E> x = last; x != null; x = x.prev) {
                index--;
                if (o.equals(x.item))
                    return index;
            }
        }
        return -1;
    }
```

解读：

* remove方法可以看出，移除的是从头开始遍历里第一个匹配的元素。
* indexOf也是如此，不贴代码了
* lastIndexOf还是是从尾到头进行遍历，其他一致

```java
    /**
     * Retrieves, but does not remove, the head (first element) of this list.
     *
     * @return the head of this list, or {@code null} if this list is empty
     * @since 1.5
     */
    public E peek() {
        final Node<E> f = first;
        return (f == null) ? null : f.item;
    }

    /**
     * Retrieves, but does not remove, the head (first element) of this list.
     *
     * @return the head of this list
     * @throws NoSuchElementException if this list is empty
     * @since 1.5
     */
    public E element() {
        return getFirst();
    }

    /**
     * Retrieves and removes the head (first element) of this list.
     *
     * @return the head of this list, or {@code null} if this list is empty
     * @since 1.5
     */
    public E poll() {
        final Node<E> f = first;
        return (f == null) ? null : unlinkFirst(f);
    }
    /**
     * Returns the first element in this list.
     *
     * @return the first element in this list
     * @throws NoSuchElementException if this list is empty
     */
    public E getFirst() {
        final Node<E> f = first;
        if (f == null)
            throw new NoSuchElementException();
        return f.item;
    }

    /**
     * Returns the last element in this list.
     *
     * @return the last element in this list
     * @throws NoSuchElementException if this list is empty
     */
    public E getLast() {
        final Node<E> l = last;
        if (l == null)
            throw new NoSuchElementException();
        return l.item;
    }
    /**
     * Unlinks non-null first node f.
     */
    private E unlinkFirst(Node<E> f) {
        // assert f == first && f != null;
        final E element = f.item;
        final Node<E> next = f.next;
        f.item = null;
        f.next = null; // help GC
        first = next;
        if (next == null)
            last = null;
        else
            next.prev = null;
        size--;
        modCount++;
        return element;
    }

    /**
     * Unlinks non-null last node l.
     */
    private E unlinkLast(Node<E> l) {
        // assert l == last && l != null;
        final E element = l.item;
        final Node<E> prev = l.prev;
        l.item = null;
        l.prev = null; // help GC
        last = prev;
        if (prev == null)
            first = null;
        else
            prev.next = null;
        size--;
        modCount++;
        return element;
    }
    public E peekFirst() {
        final Node<E> f = first;
        return (f == null) ? null : f.item;
     }
    public E peekLast() {
        final Node<E> l = last;
        return (l == null) ? null : l.item;
    }
    public E pollFirst() {
        final Node<E> f = first;
        return (f == null) ? null : unlinkFirst(f);
    }
    public E pollLast() {
        final Node<E> l = last;
        return (l == null) ? null : unlinkLast(l);
    }
```

解读：

* Deque相关操作，peek,element,poll都是获取头部元素，区别在于：peek()如果没有的话，返回的是null，不进行删除；element如果没有，则是抛出NoSuchElementException，不进行删除；poll则是弹出，会移除头部元素

```java
    /**
     * Retrieves and removes the head (first element) of this list.
     *
     * @return the head of this list
     * @throws NoSuchElementException if this list is empty
     * @since 1.5
     */
    public E remove() {
        return removeFirst();
    }
```

* remove删除的是第一个元素，因为队列是FIFO。删除不掉，则抛出noSuchException

```java
    /**
     * Adds the specified element as the tail (last element) of this list.
     *
     * @param e the element to add
     * @return {@code true} (as specified by {@link Queue#offer})
     * @since 1.5
     */
    public boolean offer(E e) {
        return add(e);
    }

    // Deque operations
    /**
     * Inserts the specified element at the front of this list.
     *
     * @param e the element to insert
     * @return {@code true} (as specified by {@link Deque#offerFirst})
     * @since 1.6
     */
    public boolean offerFirst(E e) {
        addFirst(e);
        return true;
    }

    /**
     * Inserts the specified element at the end of this list.
     *
     * @param e the element to insert
     * @return {@code true} (as specified by {@link Deque#offerLast})
     * @since 1.6
     */
    public boolean offerLast(E e) {
        addLast(e);
        return true;
    }
```

解读：

* 于队头或者队尾添加元素，没啥解释的

```java
    /**
     * Pushes an element onto the stack represented by this list.  In other
     * words, inserts the element at the front of this list.
     *
     * <p>This method is equivalent to {@link #addFirst}.
     *
     * @param e the element to push
     * @since 1.6
     */
    public void push(E e) {
        addFirst(e);
    }

    /**
     * Pops an element from the stack represented by this list.  In other
     * words, removes and returns the first element of this list.
     *
     * <p>This method is equivalent to {@link #removeFirst()}.
     *
     * @return the element at the front of this list (which is the top
     *         of the stack represented by this list)
     * @throws NoSuchElementException if this list is empty
     * @since 1.6
     */
    public E pop() {
        return removeFirst();
    }
```

解读：

* LinkedList惊现Stack的操作，看了下源码这两个操作是在Deque接口定义的。



问题解读：

1. Sequentia意思为：顺序，连续。AbstractSequentialList继承于AbstractList，主要看AbstractSequentiaList实现了什么方法。图就不上了，实现的方法，都是通过Iterator来进行的实现（大量用到了策略模式），而AbstractList中，使用for循环来实现的。**迭代器模式是为了提供方法访问聚合对象的元素，但是不暴露对象的内部表示**。由注释可知**This class provides a skeletal implementation of the List interface to minimize the effort required to implement this interface backed by a "sequential access" data store (such as a linked list). For random access data (such as an array), AbstractList should be used in preference to this class.**  LinkedList使用该类会有更好的性能，而如果是支持随机访问的数据，比如ArrayList，应该优先使用AbstractList，会有更好的性能。
2. LinkedList不实现RandomAccess的原因有：使用AbstractSequentialList会有更好的性能。RandomAccess对于支持随机访问的(如ArrayList)有更好的性能。与Collections的indexedBinarySearch可以看到。对于实现了RandomAccess的接口，可以用二分方法进行更好的排序，拥有更好的性能。
3. 总之，实现了RandomAccess 的接口，使用for i 循环会有更好的性能。而没有实现的，链表操作的，使用interator会有更好的性能。