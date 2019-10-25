---
title: HashSet源码

categories: 

- 源码阅读

tags: 

- JDK源码

date: 2019-07-17
---

Set和List的区别就是，List是有序的，可重复的，而Set是无序的，不可重复的，但是为什呢，从来没有看过，这次来看下源码，看源码中是怎么来实现的

<!-- more -->

```java
    /**
     * Constructs a new, empty set; the backing {@code HashMap} instance has
     * default initial capacity (16) and load factor (0.75).
     */
    public HashSet() {
        map = new HashMap<>();
    }
```

真简单。。。本来还想得说不定也得和HashMap一样写那么多，弄了半天就这么一句话就解释了。

```java
/**
     * Adds the specified element to this set if it is not already present.
     * More formally, adds the specified element {@code e} to this set if
     * this set contains no element {@code e2} such that
     * {@code Objects.equals(e, e2)}.
     * If this set already contains the element, the call leaves the set
     * unchanged and returns {@code false}.
     *
     * @param e element to be added to this set
     * @return {@code true} if this set did not already contain the specified
     * element
     */
public boolean add(E e) {
  return map.put(e, PRESENT)==null;
}
private static final Object PRESENT = new Object();
 public boolean remove(Object o) {
   return map.remove(o)==PRESENT;
 }
```

使用的是HashMap，key实际上就是set里存的值，value就是一个object。而键值对是一一对应的，也就解释了为什么Set的值是不可重复的。

而由于说Set继承于Collection，而Collection接口又继承了Iterator接口，所以说，Set也是支持迭代器操作的，也就支持Foreach操作。

至于说为什么无序，因为HashMap的key并不能保证有序。hashmap按照散列值进行存储，读取的顺序还真不一定是插入的顺序。