---
title: TreeMap源码

categories: 

- 源码阅读

tags: 

- JDK源码


---

> Map的另一种有序实现，上一种是LinkedHashMap

```java
/**
 * A Red-Black tree based {@link NavigableMap} implementation.
 * The map is sorted according to the {@linkplain Comparable natural
 * ordering} of its keys, or by a {@link Comparator} provided at map
 * creation time, depending on which constructor is used.
 *
 * <p>This implementation provides guaranteed log(n) time cost for the
 * {@code containsKey}, {@code get}, {@code put} and {@code remove}
 * operations.  Algorithms are adaptations of those in Cormen, Leiserson, and
 * Rivest's <em>Introduction to Algorithms</em>.
 */
//简单翻译一下，摘录主要的东西，基于红黑树的实现，这个map的key是自然有序的，或者自定义实现在使用构造方法创建的时候使用Comparator
//这个实现支持log(n)级别的时间复杂度，包裹containsKey,get,put,remove等操作。后面是算法改编自XXX。
//这个map是不同步的，如果想多线程使用，需要使用Collections.syschronizedSortedMap包装
    /**
     * Constructs a new, empty tree map, ordered according to the given
     * comparator.  All keys inserted into the map must be <em>mutually
     * comparable</em> by the given comparator: {@code comparator.compare(k1,
     * k2)} must not throw a {@code ClassCastException} for any keys
     * {@code k1} and {@code k2} in the map.  If the user attempts to put
     * a key into the map that violates this constraint, the {@code put(Object
     * key, Object value)} call will throw a
     * {@code ClassCastException}.
     *
     * @param comparator the comparator that will be used to order this map.
     *        If {@code null}, the {@linkplain Comparable natural
     *        ordering} of the keys will be used.
     */
    public TreeMap(Comparator<? super K> comparator) {
        this.comparator = comparator;
    }
//比较关键的一个代码，创建的时候，指定排序方式，再插入的时候进行排序，如果为null，那就会使用默认的排序方式
    /**
     * Constructs a new tree map containing the same mappings and
     * using the same ordering as the specified sorted map.  This
     * method runs in linear time.
     *
     * @param  m the sorted map whose mappings are to be placed in this map,
     *         and whose comparator is to be used to sort this map
     * @throws NullPointerException if the specified map is null
     */
    public TreeMap(SortedMap<K, ? extends V> m) {
        comparator = m.comparator();
        try {
            buildFromSorted(m.size(), m.entrySet().iterator(), null, null);
        } catch (java.io.IOException | ClassNotFoundException cannotHappen) {
        }
    }
//这个代码交待我们，如果构造方法传入的另一个TreeMap的话，那么排序方式会继承之前的map
//containsKey，containsValue,get,put这些全部是基于红黑树，没必要说，懂了红黑树的数据结构，就自然明白了
//非HashMap，不是基于hash算法
//感觉没啥写的了。。。红黑树现在感觉也不难。。。简直了
```

