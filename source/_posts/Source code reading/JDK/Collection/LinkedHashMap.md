---
title: LinkedHashMap源码

categories: 

- 源码阅读

tags: 

- JDK源码
---

写在前面

HashMap是无序的，那么说，Map接口有没有说有序的实现？有，两种，一种LinkedHashMap，一种就是TreeMap，先来看看LinkedHashMap。

照旧，先来翻译一下LinkedHashMap的注释。好长啊，Map的注释怎么就这么长。

```java
/**
 * <p>Hash table and linked list implementation of the {@code Map} interface,
 * with predictable iteration order.  This implementation differs from
 * {@code HashMap} in that it maintains a doubly-linked list running through
 * all of its entries.  This linked list defines the iteration ordering,
 * which is normally the order in which keys were inserted into the map
 * (<i>insertion-order</i>).  Note that insertion order is not affected
 * if a key is <i>re-inserted</i> into the map.  (A key {@code k} is
 * reinserted into a map {@code m} if {@code m.put(k, v)} is invoked when
 * {@code m.containsKey(k)} would return {@code true} immediately prior to
 * the invocation.)
 
 * Map散列表和链表的实现，具有可预测的特性。这个实现和HashMap不同的地方在于它维护了一个贯穿所有实体的双向链表，这个链表定义了其迭代顺序，每个key再插入map之后都是有序的。如果一个key已经插入到了map中，再次插入的时候，是不影响其在map中的顺序的。（当一个key重新插入map的时候，如果在m.containsKey(k) 在调用之前将要return true的时候调用m.put(k,v)。）
 
 * <p>This implementation spares its clients from the unspecified, generally
 * chaotic ordering provided by {@link HashMap} (and {@link Hashtable}),
 * without incurring the increased cost associated with {@link TreeMap}.  It
 * can be used to produce a copy of a map that has the same order as the
 * original, regardless of the original map's implementation: 
 
  这个实现避免了像HashMap和HashTable的实现一样不确定，难以预测。并且不需要像TreeMap一样一直增加的消耗。它可以复制一个map来使用，它会和原先的map一样有同样的顺序，而不用管之前源map的实现。就像下面这样
  
 * <pre>
 *     void foo(Map m) {
 *         Map copy = new LinkedHashMap(m);
 *         ...
 *     }
 * </pre>
 * This technique is particularly useful if a module takes a map on input,
 * copies it, and later returns results whose order is determined by that of
 * the copy.  (Clients generally appreciate having things returned in the same
 * order they were presented.)
 
 * 这个技巧是非常有用的，如果一个模块获得了个map，然后复制它，过一会就回返回一个顺序已经被定义的副本(用户通常中意一件事像原先那样发展)
 
 * <p>A special {@link #LinkedHashMap(int,float,boolean) constructor} is
 * provided to create a linked hash map whose order of iteration is the order
 * in which its entries were last accessed, from least-recently accessed to
 * most-recently (<i>access-order</i>).  
 	一个特殊的构造方法被提供来创建一个Linked hash map，其迭代顺序是其entry上次访问的顺序，从最近的访问到最多的访问(访问排序)
 
 This kind of map is well-suited to
 * building LRU caches.  Invoking the {@code put}, {@code putIfAbsent},
 * {@code get}, {@code getOrDefault}, {@code compute}, {@code computeIfAbsent},
 * {@code computeIfPresent}, or {@code merge} methods results
 * in an access to the corresponding entry (assuming it exists after the
 * invocation completes). The {@code replace} methods only result in an access
 * of the entry if the value is replaced.  The {@code putAll} method generates one
 * entry access for each mapping in the specified map, in the order that
 * key-value mappings are provided by the specified map's entry set iterator.
 * <i>No other methods generate entry accesses.</i>  In particular, operations
 * on collection-views do <i>not</i> affect the order of iteration of the
 * backing map.
 * 这种类型的map非常适合于构建一个LRU缓存(Least Recently Used),使用put,putIfAbsent,get,getOrDefault,apmpute,aomputeIfAbsent,aomputeInfPresent
 merge等方法来记录访问缓存(在调用完成之后记录)。如果value被替换的话，replace方法仅仅返回entry中被访问过的对象，putAll方法生成一个访问记录给每个在这个map里的entry，在这种情况下，这个key-value映射将会被支持于map的set iterator，没有其他方法生成entry 访问记录。通常情况下，集合视图的操作不会修改map的iterator的顺序。
 * <p>The {@link #removeEldestEntry(Map.Entry)} method may be overridden to
 * impose a policy for removing stale mappings automatically when new mappings
 * are added to the map.
 *
 removeEldestEntry这个方法可能被重载来在remove的时候或者在新的映射添加到map的时候。安全自动的使用
 
 * <p>This class provides all of the optional {@code Map} operations, and
 * permits null elements.  Like {@code HashMap}, it provides constant-time performance for the basic operations ({@code add}, {@code contains} and
 * {@code remove}), assuming the hash function disperses elements
 * properly among the buckets. 
 LinkedHashMap支持Map的所有操作，并且允许null元素。像HashMap，假设这个hash方法均匀分布key的话，它的add，contains，remove等基础操作拥有稳定的性能。
 * Performance is likely to be just slightly
 * below that of {@code HashMap}, due to the added expense of maintaining the
 * linked list, with one exception: Iteration over the collection-views
 * of a {@code LinkedHashMap} requires time proportional to the <i>size</i>
 * of the map, regardless of its capacity.  Iteration over a {@code HashMap}
 * is likely to be more expensive, requiring time proportional to its
 * <i>capacity</i>.
 * 
 	LinkedHashMap的性能仅仅稍微比HashMap低一点，由于添加操作使用的是Linked list，所以性能消耗全在这里，除了一个情况，迭代LinkedHashMap的集合试图需要O(map.size)，不管他的capacity多大。而迭代HashMap的时候与它很像，但是需要更多的花费，需要O(map.capacity)的花费。
 	
 * <p>A linked hash map has two parameters that affect its performance:
 * <i>initial capacity</i> and <i>load factor</i>.  They are defined precisely
 * as for {@code HashMap}.  Note, however, that the penalty for choosing an
 * excessively high value for initial capacity is less severe for this class
 * than for {@code HashMap}, as iteration times for this class are unaffected
 * by capacity.
 *
 	一个LinkedHashMap有连个参数影响他的性能(initial capacity , load factor),他们在HashMap中被精确定义。但是需要注意的是，相对于HashMap来说，选择一个非常大的initial capacity的值产生的影响是非常小的，LinkedHashMap的迭代时间并不受capacity的影响。
 
 * <p><strong>Note that this implementation is not synchronized.</strong>
 * If multiple threads access a linked hash map concurrently, and at least
 * one of the threads modifies the map structurally, it <em>must</em> be
 * synchronized externally.  This is typically accomplished by
 * synchronizing on some object that naturally encapsulates the map.
 *
 	注意：这个实现并不是线程安全的，如果两个以上的线程并发访问LinkedHashMap，同时至少有一个线程修改了map的结构，外部操作必须是线程安全的，这通常使用某个对象的同步操作来包装这个map。
 
 * If no such object exists, the map should be "wrapped" using the
 * {@link Collections#synchronizedMap Collections.synchronizedMap}
 * method.  This is best done at creation time, to prevent accidental
 * unsynchronized access to the map:<pre>
 *   Map m = Collections.synchronizedMap(new LinkedHashMap(...));</pre>
 *
 如果没有对象存在的话，那么这个map应该使用Collections.synchronizedMap来包装。它最好在创建的时候使用，来防止线程不安全的访问。
 
 * A structural modification is any operation that adds or deletes one or more
 * mappings or, in the case of access-ordered linked hash maps, affects
 * iteration order.  In insertion-ordered linked hash maps, merely changing
 * the value associated with a key that is already contained in the map is not
 * a structural modification.  <strong>In access-ordered linked hash maps,
 * merely querying the map with {@code get} is a structural modification.
 * </strong>)
 *
 * <p>The iterators returned by the {@code iterator} method of the collections
 * returned by all of this class's collection view methods are
 * <em>fail-fast</em>: if the map is structurally modified at any time after
 * the iterator is created, in any way except through the iterator's own
 * {@code remove} method, the iterator will throw a {@link
 * ConcurrentModificationException}.  Thus, in the face of concurrent
 * modification, the iterator fails quickly and cleanly, rather than risking
 * arbitrary, non-deterministic behavior at an undetermined time in the future.
 *
 * <p>Note that the fail-fast behavior of an iterator cannot be guaranteed
 * as it is, generally speaking, impossible to make any hard guarantees in the
 * presence of unsynchronized concurrent modification.  Fail-fast iterators
 * throw {@code ConcurrentModificationException} on a best-effort basis.
 * Therefore, it would be wrong to write a program that depended on this
 * exception for its correctness:   <i>the fail-fast behavior of iterators
 * should be used only to detect bugs.</i>
 *
 * <p>The spliterators returned by the spliterator method of the collections
 * returned by all of this class's collection view methods are
 * <em><a href="Spliterator.html#binding">late-binding</a></em>,
 * <em>fail-fast</em>, and additionally report {@link Spliterator#ORDERED}.
 *
 * <p>This class is a member of the
 * <a href="{@docRoot}/java.base/java/util/package-summary.html#CollectionsFramework">
 * Java Collections Framework</a>.
 *
 * @implNote
 * The spliterators returned by the spliterator method of the collections
 * returned by all of this class's collection view methods are created from
 * the iterators of the corresponding collections.
 *
 * @param <K> the type of keys maintained by this map
 * @param <V> the type of mapped values
 *
 * @author  Josh Bloch
 * @see     Object#hashCode()
 * @see     Collection
 * @see     Map
 * @see     HashMap
 * @see     TreeMap
 * @see     Hashtable
 * @since   1.4
 */
	
```

