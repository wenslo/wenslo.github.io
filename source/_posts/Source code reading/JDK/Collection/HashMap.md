---
title: HashMap源码

categories: 

- 源码阅读

tags: 

- JDK源码


---

> 阅读源码之前还是应该阅读注释

API文档：

Hash table based implementation of the Map interface. This implementation provides all of the optional map operations, and permits null values and the null key. (The HashMap class is roughly equivalent to Hashtable, except that it is unsynchronized and permits nulls.) This class makes no guarantees as to the order of the map; in particular, it does not guarantee that the order will remain constant over time.

HashTable是Map的基础实现，这个实现支持map的所有操作，而且允许null的value和key（HashMap除了它支持null且线程不安全外和Hashtable差不多），HashMap不保证map里的有序性，在一些情况，它不保证一段时间内的有序性。

This implementation provides constant-time performance for the basic operations (get and put), assuming the hash function disperses the elements properly among the buckets. Iteration over collection views requires time proportional to the "capacity" of the HashMap instance (the number of buckets) plus its size (the number of key-value mappings). Thus, it's very important not to set the initial capacity too high (or the load factor too low) if iteration performance is important.

HashMap的get和put拥有常数时间的性能，假定hash函数将元素均匀分散于桶(bucket)之间，迭代集合需要有和HashMap实例的capacity(容量，桶的容量)+map的大小（key->value映射的数量）成比例的时间。因此，如果性能是非常重要的话，将初始容量(initial capacity)设置的不要太高是非常重要的一件事，

An instance of HashMap has two parameters that affect its performance: initial capacity and load factor. The capacity is the number of buckets in the hash table, and the initial capacity is simply the capacity at the time the hash table is created. The load factor is a measure of how full the hash table is allowed to get before its capacity is automatically increased. When the number of entries in the hash table exceeds the product of the load factor and the current capacity, the hash table is rehashed (that is, internal data structures are rebuilt) so that the hash table has approximately twice the number of buckets.

HashMap的示例有两个参数影响它的性能：初始容量和加载因子（initial capacity and load factor），这个容量是hash table里桶（bucket）的数量。然后这个初始容量（initial capacity）仅仅是在hash table创建的时候的容量。这个加载因子(load factor)是hash table在它的容量自动增加之前测量增加量的一个关键参数。当hash table中的entry的数量超过(load factor * current capacity)的时候，hash table 会进行重新hash(意思是，内部的数据结构会重新构建)，构建之后这个hash table 的桶的数量差不多有之前桶的数量的两倍

As a general rule, the default load factor (.75) offers a good tradeoff between time and space costs. Higher values decrease the space overhead but increase the lookup cost (reflected in most of the operations of the HashMap class, including get and put). The expected number of entries in the map and its load factor should be taken into account when setting its initial capacity, so as to minimize the number of rehash operations. If the initial capacity is greater than the maximum number of entries divided by the load factor, no rehash operations will ever occur.

在一般的条件下，默认的加载因子在时间和空间消耗之间做了很好的权衡。负载因子的值变高了之后会减少空间的消耗，但是会增加查找的开销(表现在HashMap中的大部分操作，包括get和set)。map里entries的数量和加载因子在初始容量被设置的时候就应该被考虑到。所以减少rehash的次数。如果初始化容量大于entries的数量除以负载因子，那么不会发生rehash操作。

If many mappings are to be stored in a HashMap instance, creating it with a sufficiently large capacity will allow the mappings to be stored more efficiently than letting it perform automatic rehashing as needed to grow the table. 

如果很多映射被存储于HashMap实例。那么创建一个有足够大的容量的实例将会比让它自动去执行rehash来扩大表来进行映射存储来的的更高效（efficientily 有效，有效率，效率高）。

Note that using many keys with the same hashCode() is a sure way to slow down performance of any hash table. 

注意：任何HashTable如果很多key有了相同的hashCode的话将会降低其性能。

To ameliorate impact, when keys are Comparable, this class may use comparison order among keys to help break ties.

如果改善其影响的话，当key是可比较的时候，这个类也许可以使用key之间的比较关系来帮助打破关系

Note that this implementation is not synchronized. If multiple threads access a hash map concurrently, and at least one of the threads modifies the map structurally, it must be synchronized externally. (A structural modification is any operation that adds or deletes one or more mappings; merely changing the value associated with a key that an instance already contains is not a structural modification.) 

注意：这个实现是线程不安全的，如果很多线程同时访问一个hash map的话，并且至少有一个线程修改了该map 的结构，那么它在外部必须是线程安全的操作。(结构改变是指任何添加或删除一个或者多个映射的操作。仅仅修改一个实例里key相关的value的话，已经包含的并不是结构修改)

This is typically accomplished by synchronizing on some object that naturally encapsulates the map.

这通常由一些对象的同步操作来完成自然封装的的map。

 If no such object exists, the map should be "wrapped" using the Collections.synchronizedMap method. 

如果没有任何对象存在，这个map应该使用Collections.synchronizedMap来进行包装。

This is best done at creation time, to prevent accidental unsynchronized access to the map:

​     Map m = Collections.synchronizedMap(new HashMap(...));

最好是在创建的时候完成这个操作，以防止意外线程不安全的访问该map

The iterators returned by all of this class's "collection view methods" are fail-fast: if the map is structurally modified at any time after the iterator is created, in any way except through the iterator's own remove method, the iterator will throw a ConcurrentModificationException. 

这个迭代器范回的是collection的映射元素，会抛出fail-fast异常。如果这个map结构被修改于任何时间在迭代器被创建之后，用任何方式除了迭代器自己的remove方法，这个迭代器都会抛出 快速失败异常

Thus, in the face of concurrent modification, the iterator fails quickly and cleanly, rather than risking arbitrary, non-deterministic behavior at an undetermined time in the future.

因此。面对并发修改的时候，迭代器会快速而清晰的失败，而不是顶着是任意的风向和不确定的的行为在未知的时间

Note that the fail-fast behavior of an iterator cannot be guaranteed as it is, generally speaking, impossible to make any hard guarantees in the presence of unsynchronized concurrent modification. 

注意：迭代器的fail-fast行为不能像这样得到保障，一般来说，不可能硬性保证其一定出现在线程不安全的并发修改下

Fail-fast iterators throw ConcurrentModificationException on a best-effort basis. Therefore, it would be wrong to write a program that depended on this exception for its correctness: the fail-fast behavior of iterators should be used only to detect bugs.

迭代器的Fail-fast尽最大努力在抛出ConcurrentModificationException，因此，为了正确性，需要编写一个程序，依赖于这个异常，来检验其错误。迭代器的fail-fast行为应该仅仅用于发现bug

This class is a member of the Java Collections Framework.

HashMap是Java集合框架下的成员。

————————————————————————————————————————————————————

分割线

Implementation notes.

This map usually acts as a binned (bucketed) hash table, but when bins get too large, they are transformed into bins of
TreeNodes, each structured similarly to those in java.util.TreeMap. 

这个map通常为用桶（bucketed）实现的hash table，但是当桶的数量太大的时候，它们会转换成TreeNode，每个数据结构都与TreeMap类似。

Most methods try to use normal bins, but relay to TreeNode methods when applicable (simply by checking
instanceof a node).  Bins of TreeNodes may be traversed and used like any others, but additionally support faster lookup when overpopulated. 

大多数方法都尽量使用普通的桶，但是当可以的时候（仅仅检查bin是否能够 instaneof node），就会转变成为TreeNode。桶或TreeNodes应该可以遍历而且用起来和其他的一致，但是进一步的支持快速的循环当数据过多的时候。

However, since the vast majority of bins in normal use are not overpopulated, checking for existence of
tree bins may be delayed in the course of table methods.

然后，通常桶的大多数使用都是在数据不多的时候。在使用table的方法的时候可能延迟检查桶是否存在

Tree bins (i.e., bins whose elements are all TreeNodes) are ordered primarily by hashCode, but in the case of ties, if two
elements are of the same "class C implements Comparable<C>", type then their compareTo method is used for ordering. (We conservatively check generic types via reflection to validate this -- see method comparableClassFor).  The added complexity of tree bins is worthwhile in providing worst-case O(log n) operations when keys either have distinct hashes or are orderable, Thus, performance degrades gracefully under accidental or malicious usages in which hashCode() methods return values that are poorly distributed, as well as those in which many keys share a hashCode, so long as they are also Comparable. (If neither of these apply, we may waste about a factor of two in time and space compared to taking no precautions. But the only known cases stem from poor user programming practices that are already so slow that this makes little difference.)

树桶(桶里所有的元素都是TreeNodes)主要是根据hashCode排序的，但是在关联的情况下，如果两个元素是同样的类，都实现了Comparable接口，然后他们进行排序(我们保守检查类的类型通过反射来验证--see method co-mparableClassF-or)。

Because TreeNodes are about twice the size of regular nodes, we
use them only when bins contain enough nodes to warrant use
(see TREEIFY_THRESHOLD). And when they become too small (due to
removal or resizing) they are converted back to plain bins.  In
usages with well-distributed user hashCodes, tree bins are
rarely used.  Ideally, under random hashCodes, the frequency of
nodes in bins follows a Poisson distribution
(http://en.wikipedia.org/wiki/Poisson_distribution) with a
parameter of about 0.5 on average for the default resizing
threshold of 0.75, although with a large variance because of
resizing granularity. Ignoring variance, the expected
occurrences of list size k are (exp(-0.5) * pow(0.5, k) /
factorial(k)). The first values are:

0:    0.60653066
1:    0.30326533
2:    0.07581633
3:    0.01263606
4:    0.00157952
5:    0.00015795
6:    0.00001316
7:    0.00000094
8:    0.00000006
more: less than 1 in ten million

The root of a tree bin is normally its first node.  However,
sometimes (currently only upon Iterator.remove), the root might
be elsewhere, but can be recovered following parent links
(method TreeNode.root()).

All applicable internal methods accept a hash code as an
argument (as normally supplied from a public method), allowing
them to call each other without recomputing user hashCodes.
Most internal methods also accept a "tab" argument, that is
normally the current table, but may be a new or old one when
resizing or converting.

When bin lists are treeified, split, or untreeified, we keep
them in the same relative access/traversal order (i.e., field
Node.next) to better preserve locality, and to slightly
simplify handling of splits and traversals that invoke
iterator.remove. When using comparators on insertion, to keep a
total ordering (or as close as is required here) across
rebalancings, we compare classes and identityHashCodes as
tie-breakers.

The use and transitions among plain vs tree modes is
complicated by the existence of subclass LinkedHashMap. See
below for hook methods defined to be invoked upon insertion,
removal and access that allow LinkedHashMap internals to
otherwise remain independent of these mechanics. (This also
requires that a map instance be passed to some utility methods
that may create new nodes.)

The concurrent-programming-like SSA-based coding style helps
avoid aliasing errors amid all of the twisty pointer operations.

