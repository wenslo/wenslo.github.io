---
title: ConcurrentHashMap源码

categories: 

- 源码阅读

tags: 

- JDK源码


date: 2019-08-25
---

> ConcurrentHashMap，可以进行并发读，写操作互斥。HashMap是线程不安全的。

先翻译一下重点注释

<!-- more -->

```java
    /*
     * Overview:
     *
     * The primary design goal of this hash table is to maintain
     * concurrent readability (typically method get(), but also
     * iterators and related methods) while minimizing update
     * contention. Secondary goals are to keep space consumption about
     * the same or better than java.util.HashMap, and to support high
     * initial insertion rates on an empty table by many threads.
     这个主要设计目标是为了保证并发读的最小竞争性(主要是get方法，但是也支持迭代器相关方法)，第二个目标是为了和hashMap一致或者更好的空间消耗，并且支持多线程下的空表的高并发初始化插入速度。
     * The table is lazily initialized to a power-of-two size upon the
     * first insertion.  Each bin in the table normally contains a
     * list of Nodes (most often, the list has only zero or one Node).
     * Table accesses require volatile/atomic reads, writes, and
     * CASes.  Because there is no other way to arrange this without
     * adding further indirections, we use intrinsics
     * (jdk.internal.misc.Unsafe) operations.
     这个table是在首次插入之后进行延迟初始化的，大小为2的次方数，每个table中的子节点通常都包含一个node的list(大多数情况下，这个list仅仅只有一个Node或者没有Node)。访问tabe需要volatile/atomic 读，写和CAS操作。因为这里没有其他方法可以再不使用间接指令的情况下安排table，所以我们使用内在函数(Unsafe)操作。
     * We use the top (sign) bit of Node hash fields for control
     * purposes -- it is available anyway because of addressing
     * constraints.  Nodes with negative hash fields are specially
     * handled or ignored in map methods.
     //我们使用Node参与hash的字段的top位来达到控制的目的，由于地址约束的存在，它无论如何都是欧东可用的，Node的辣鸡hash 字段会被在map的方法里面特殊处理或者直接丢弃
     * Insertion (via put or its variants) of the first node in an
     * empty bin is performed by just CASing it to the bin.  This is
     * by far the most common case for put operations under most
     * key/hash distributions.  Other update operations (insert,
     * delete, and replace) require locks.  We do not want to waste
     * the space required to associate a distinct lock object with
     * each bin, so instead use the first node of a bin list itself as
     * a lock. Locking support for these locks relies on builtin
     * "synchronized" monitors.
     在一个空桶中的首个node的插入操作(通过put或者它的变种方法)中，是通过CAS操作进行的。对于大多数的key/value分布情况下，这是最普通最常见的情况。其他修改操作需要锁(insert,delete,replace)，我们不去浪费空间在每个桶中挂上不同独立的锁，所以使用桶中第一个节点来作为锁，这些锁的锁定支持，依赖于内置的“同步”监视器。
     * Using the first node of a list as a lock does not by itself
     * suffice though: When a node is locked, any update must first
     * validate that it is still the first node after locking it, and
     * retry if not. Because new nodes are always appended to lists,
     * once a node is first in a bin, it remains first until deleted
     * or the bin becomes invalidated (upon resizing).
     使用list中的首个节点来作为锁并不能够满足条件：当一个node被锁的时候，任何修改都必须在锁定之后进行首节点验证，验证它仍然是首节点。而且如果不是首节点就重新验证。因为时新的node通常都是添加到list里，第一次的时候node是桶里的第一个元素，它会保持第一知道删除或者桶变成不可用的状态（调整大小后）。
     * The main disadvantage of per-bin locks is that other update
     * operations on other nodes in a bin list protected by the same
     * lock can stall, for example when user equals() or mapping
     * functions take a long time.  However, statistically, under
     * random hash codes, this is not a common problem.  Ideally, the
     * frequency of nodes in bins follows a Poisson distribution
     * (http://en.wikipedia.org/wiki/Poisson_distribution) with a
     * parameter of about 0.5 on average, given the resizing threshold
     * of 0.75, although with a large variance because of resizing
     * granularity. Ignoring variance, the expected occurrences of
     * list size k are (exp(-0.5) * pow(0.5, k) / factorial(k)). The
     * first values are:
     *
     * 0:    0.60653066
     * 1:    0.30326533
     * 2:    0.07581633
     * 3:    0.01263606
     * 4:    0.00157952
     * 5:    0.00015795
     * 6:    0.00001316
     * 7:    0.00000094
     * 8:    0.00000006
     * more: less than 1 in ten million
     *
     * Lock contention probability for two threads accessing distinct
     * elements is roughly 1 / (8 * #elements) under random hashes.
     *
     当桶上锁时，桶中其他节点的其他操作会中止，比如使用equals或者mapping的方法将会执行很长时间。然后在统计中，在随机hash值的情况下，这不是一个常见的问题。理想情况下，在给定阈值给0.75的时候，桶中节点的分布遵循平均值约为0.5的泊松分布，尽管会因为resizing粒度具有较大方差，忽略方差，list的期望大小k为(exp(-0.5) * pow(0.5, k) / factorial(k))
     两个线程访问不同的元素，锁竞争的概率大约为 1 / (* elements)
     * Actual hash code distributions encountered in practice
     * sometimes deviate significantly from uniform randomness.  This
     * includes the case when N > (1<<30), so some keys MUST collide.
     * Similarly for dumb or hostile usages in which multiple keys are
     * designed to have identical hash codes or ones that differs only
     * in masked-out high bits. So we use a secondary strategy that
     * applies when the number of nodes in a bin exceeds a
     * threshold. These TreeBins use a balanced tree to hold nodes (a
     * specialized form of red-black trees), bounding search time to
     * O(log N).  Each search step in a TreeBin is at least twice as
     * slow as in a regular list, but given that N cannot exceed
     * (1<<64) (before running out of addresses) this bounds search
     * steps, lock hold times, etc, to reasonable constants (roughly
     * 100 nodes inspected per operation worst case) so long as keys
     * are Comparable (which is very common -- String, Long, etc).
     * TreeBin nodes (TreeNodes) also maintain the same "next"
     * traversal pointers as regular nodes, so can be traversed in
     * iterators in the same way.
```

```java
	 /**
     * The array of bins. Lazily initialized upon first insertion.
     * Size is always a power of two. Accessed directly by iterators.
     */
    transient volatile Node<K,V>[] table;

    /**
     * The next table to use; non-null only while resizing.
     */
    private transient volatile Node<K,V>[] nextTable;

    /**
     * Base counter value, used mainly when there is no contention,
     * but also as a fallback during table initialization
     * races. Updated via CAS.
     */
    private transient volatile long baseCount;

    /**
     * Table initialization and resizing control.  When negative, the
     * table is being initialized or resized: -1 for initialization,
     * else -(1 + the number of active resizing threads).  Otherwise,
     * when table is null, holds the initial table size to use upon
     * creation, or 0 for default. After initialization, holds the
     * next element count value upon which to resize the table.
     */
    private transient volatile int sizeCtl;

    /**
     * The next table index (plus one) to split while resizing.
     */
    private transient volatile int transferIndex;

    /**
     * Spinlock (locked via CAS) used when resizing and/or creating CounterCells.
     */
    private transient volatile int cellsBusy;

    /**
     * Table of counter cells. When non-null, size is a power of 2.
     */
    private transient volatile CounterCell[] counterCells;
```

这里就发现，所有的变量都是volatile的，而volatile这个关键字的作用，就是首先保证可见性，然后禁止指令重排序，单次读写的原子性

