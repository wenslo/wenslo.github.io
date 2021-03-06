---
title: HashMap源码

categories: 

- 源码阅读

tags: 

- JDK源码

mathjax: true
date: 2019-05-10
description: hashmap
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
elements are of the same "class C implements Comparable<C>", type then their compareTo method is used for ordering. (We conservatively check generic types via reflection to validate this -- see method comparableClassFor).  

树桶(桶里所有的元素都是TreeNodes)主要是根据hashCode排序的，但是在关联的情况下，如果两个元素是同样的类，都实现了Comparable接口，然后他们进行排序(我们保守检查类的类型通过反射来验证--see method comparableClassF-or)。

The added complexity of tree bins is worthwhile in providing worst-case O(log n) operations when keys either have distinct hashes or are orderable, Thus, performance degrades gracefully under accidental or malicious usages in which hashCode() methods return values that are poorly distributed, as well as those in which many keys share a hashCode, so long as they are also Comparable. (If neither of these apply, we may waste about a factor of two in time and space compared to taking no precautions. But the only known cases stem from poor user programming practices that are already so slow that this makes little difference.)

当key有不同的hashCode或者说是已排序的话，树桶（翻译很别扭，有时间看看别人怎么翻译的）的添加操作的最坏时间复杂度是O(log n)。然后，在hashCode()的意外或异常执行，产生不均匀的分布的话，性能将会平缓降级，除此之外，许多key共享一个hashCode，只要是可比较的。(如果两种情况都无法匹配的话，我们将会浪费factor两倍的时间和空间性能相对于不采取预防措施。但是这种仅知的情况来源于糟糕的用户代码实践，它们已经非常缓慢了，所以基本没有什么去呗)

Because TreeNodes are about twice the size of regular nodes, we use them only when bins contain enough nodes to warrant use (see TREEIFY_THRESHOLD). 

因为TreeNodes的大小事普通节点的两倍，我们仅仅在桶里包含足够的节点的时候去保证使用它(参见 树化阈值--TREEIFY_THRESHOLD)

And when they become too small (due to removal or resizing) they are converted back to plain bins.

然后当它是比较小的时候(由于删除或者大小改变)，它将退化为普通箱。

  In usages with well-distributed user hashCodes, tree bins are rarely used.  Ideally, under random hashCodes, the frequency of nodes in bins follows a Poisson distribution (http://en.wikipedia.org/wiki/Poisson_distribution) with a
parameter of about 0.5 on average for the default resizing threshold of 0.75, although with a large variance because of
resizing granularity. Ignoring variance, the expected occurrences of list size k are (exp(-0.5) * pow(0.5, k) / factorial(k)). The first values are:

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

在拥有良好分布的hashCode的时候，树桶很少被使用。通常情况，在随机的hashCode情况中，树桶的节点分布遵循于Poisson distribution 分布方式（泊松分布），参数在在默认阈值0.75的条件下，平均数为0.5，尽管因为重新调整粒度有很大的方差变化。预计list的大小k公式是：$$10^{-0.5} 0.5^k/k!$$

概率小于千万分之一

The root of a tree bin is normally its first node.  However, sometimes (currently only upon Iterator.remove), the root might be elsewhere, but can be recovered following parent links (method TreeNode.root()).

树桶的root节点通常都是它的第一个节点。然而，有些时候(当前已知的情况就是Interator的remove)，root可能会不是第一个节点，但是可以被父链接节点所恢复(TreeNode.root())

All applicable internal methods accept a hash code as an argument (as normally supplied from a public method), allowing them to call each other without recomputing user hashCodes. Most internal methods also accept a "tab" argument, that is normally the current table, but may be a new or old one when resizing or converting.

所有可使用的内部方法接受hash code作为参数(还有被支持的公开方法)，允许他们互相调用，且不进行重新计算hashCode。大多数内部方法通常接受tab这个参数，通常代表当前table，但是也可能是新的或者旧的当大小改变或者被覆盖的时候。

When bin lists are treeified, split, or untreeified, we keep them in the same relative access/traversal order (i.e., field
Node.next) to better preserve locality, and to slightly simplify handling of splits and traversals that invoke iterator.remove. When using comparators on insertion, to keep a total ordering (or as close as is required here) across
rebalancings, we compare classes and identityHashCodes as tie-breakers.

当桶的列表为树，分隔，或者非树的时候，我们保证他们在相同的访问/遍历顺序时能更好的保留位置。并且简单的处理split和遍历在调用iterator.remove的时候。当在插入的时候使用比较器，来在重新平衡的过程中保证总体是有序的(或者按要求关闭？)，我们使用类和hashcode作为连接断开器。

The use and transitions among plain vs tree modes is complicated by the existence of subclass LinkedHashMap. See
below for hook methods defined to be invoked upon insertion,removal and access that allow LinkedHashMap internals to otherwise remain independent of these mechanics. (This also requires that a map instance be passed to some utility methods that may create new nodes.)

由于存在子类LinkedHashMap，普通模式和树模式之间的使用和转变会变得复杂。参考钩子方法定义在了调用插入，删除和访问的时候，来允许LinkedHashMap使用其他方式来保证独立性(也同样需要这一个映射实例来传递一些有用的可能创建新节点的方法)。

The concurrent-programming-like SSA-based coding style helps avoid aliasing errors amid all of the twisty pointer operations.

这个并行编码方式用于SSA的编码风格的话有助于避免混乱错误在一些指针操作上面。



————————————————————————————————————————————————————

分割线。最长的注释就在上面，现在开始代码的阅读，产生的问题，会收集在另一篇文章里

```java
/**
 * The default initial capacity - MUST be a power of two.
 */
 //默认初始化大小必须为1左移4位，也就是2^4，也就是16，必须是2的倍数
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16

/**
 * The maximum capacity, used if a higher value is implicitly specified
 * by either of the constructors with arguments.
 * MUST be a power of two <= 1<<30.
 */
 //最大容量，如果需要更大的容量，需要在构造方法中通过参数定义，必须是2的倍数，并且<= 2^30
static final int MAXIMUM_CAPACITY = 1 << 30;

/**
 * The load factor used when none specified in constructor.
 */
//当构造方法不进行指定的时候，默认加载因子为0.75f
static final float DEFAULT_LOAD_FACTOR = 0.75f;

/**
 * The bin count threshold for using a tree rather than list for a
 * bin.  Bins are converted to trees when adding an element to a
 * bin with at least this many nodes. The value must be greater
 * than 2 and should be at least 8 to mesh with assumptions in
 * tree removal about conversion back to plain bins upon
 * shrinkage.
 */
//使用树而不是使用桶的数量的阈值。当在有很多元素的桶里最后添加一个元素的时候，桶会转换为树。这个值必须大于2而且应该最少有8个，以符合树移除元素后压缩转换为普通箱的假定
static final int TREEIFY_THRESHOLD = 8;

/**
 * The bin count threshold for untreeifying a (split) bin during a
 * resize operation. Should be less than TREEIFY_THRESHOLD, and at
 * most 6 to mesh with shrinkage detection under removal.
 */
//再重新设定大小操作的时候，何时进行树的分割退化操作的阈值。应该小于TREEIFY_THRESHOLD，然后最多6个匹配的在删除操作之后的收缩检测
static final int UNTREEIFY_THRESHOLD = 6;

/**
 * The smallest table capacity for which bins may be treeified.
 * (Otherwise the table is resized if too many nodes in a bin.)
 * Should be at least 4 * TREEIFY_THRESHOLD to avoid conflicts
 * between resizing and treeification thresholds.
 */
//每个可能被树化的桶的最小的链表的容量（或者说桶里的表的重新分配大小并且有太多节点的时候）
//应该至少有32的空间阈值来避免在重新分配大小和树化的时候产生的hash冲突
static final int MIN_TREEIFY_CAPACITY = 64;
/**
 * Basic hash bin node, used for most entries.  (See below for
 * TreeNode subclass, and in LinkedHashMap for its Entry subclass.)
 */
//基本hash桶节点，被大多数实体使用。看下面的TreeNode子类和LinkedHashMap都是Entry的子类
static class Node<K,V> implements Map.Entry<K,V> {
  final int hash;
  final K key;
  V value;
  Node<K,V> next;

  Node(int hash, K key, V value, Node<K,V> next) {
    this.hash = hash;
    this.key = key;
    this.value = value;
    this.next = next;
  }

  public final K getKey()        { return key; }
  public final V getValue()      { return value; }
  public final String toString() { return key + "=" + value; }

  public final int hashCode() {
    return Objects.hashCode(key) ^ Objects.hashCode(value);
  }

  public final V setValue(V newValue) {
    V oldValue = value;
    value = newValue;
    return oldValue;
  }

  public final boolean equals(Object o) {
    if (o == this)
      return true;
    if (o instanceof Map.Entry) {
      Map.Entry<?,?> e = (Map.Entry<?,?>)o;
      if (Objects.equals(key, e.getKey()) &&
          Objects.equals(value, e.getValue()))
        return true;
    }
    return false;
}
  /**
   * Computes key.hashCode() and spreads (XORs) higher bits of hash
   * to lower.  Because the table uses power-of-two masking, sets of
   * hashes that vary only in bits above the current mask will
   * always collide. (Among known examples are sets of Float keys
   * holding consecutive whole numbers in small tables.)  So we
   * apply a transform that spreads the impact of higher bits
   * downward. There is a tradeoff between speed, utility, and
   * quality of bit-spreading. Because many common sets of hashes
   * are already reasonably distributed (so don't benefit from
   * spreading), and because we use trees to handle large sets of
   * collisions in bins, we just XOR some shifted bits in the
   * cheapest possible way to reduce systematic lossage, as well as
   * to incorporate impact of the highest bits that would otherwise
   * never be used in index calculations because of table bounds.
   */
  //又是一大段注释，真不想翻译这些，不想翻译吧，有些代码又看不懂，妈的还是自己太菜了。专八在乎这？
  //计算key的hashcode，然后高位和低位进行异或。因为链表使用了两倍的掩码，hashcode的集合仅仅在当前掩码变化的时候将会一直碰撞冲突。(已知的例子,一组浮点数会在小点的表里保持连续整数。)所以我们使用异或来将高位的冲撞向下传播。这是比特扩展在速度，功能和质量之间的折中。因为许多内容hash的集合是已经合理分布了的（所以不会从扩散中受益），而且因为我们使用树来处理桶中的大量元素，我们用一个比较便宜的方法来降低系统性能损耗，就是使用异或来转换一些bit。除此之外，包含最高位的bit位，因为表边界的原因，将不会使用计算过的下标，来归并高位的碰撞。
  //也不知道翻译对不对，哪里来个大佬带带我~~~
  static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
  }
  /**
   * Returns a power of two size for the given target capacity.
   */
  //卧槽嘞，这个就更尼玛看不懂了啊，注释倒是很简单，返回两倍目标容量的大小，最大值是MAXIMUM_CAPACITY，最小值是1，这个numberOfLeadingZeros留岛问题里解答吧
  static final int tableSizeFor(int cap) {
    int n = -1 >>> Integer.numberOfLeadingZeros(cap - 1);
    return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
  }
  /**
   * The table, initialized on first use, and resized as
   * necessary. When allocated, length is always a power of two.
   * (We also tolerate length zero in some operations to allow
   * bootstrapping mechanics that are currently not needed.)
   */
 //这个table在第一次使用的时候初始化，然后大小一定会改变。当进行分配空间的时候，大小通常都会是2的倍数(我们也会允许启动执行的机器上当前非必须的一些操作，它大小为0)
  transient Node<K,V>[] table;

  /**
   * Holds cached entrySet(). Note that AbstractMap fields are used
   * for keySet() and values().
   */
  //entrySet的缓存，记录AbstractMap字段里keySet和values方法的使用
  transient Set<Map.Entry<K,V>> entrySet;
  /**
   * The number of key-value mappings contained in this map.
   */
  //key-value映射在map里的大小
  transient int size;
  /**
     * The number of times this HashMap has been structurally modified
     * Structural modifications are those that change the number of mappings in
     * the HashMap or otherwise modify its internal structure (e.g.,
     * rehash).  This field is used to make iterators on Collection-views of
     * the HashMap fail-fast.  (See ConcurrentModificationException).
     */
  //modCount是HashMap发生变化或者修改了内部结构的时候（rehash），会改变这个数字，这个字段在iterator的集合视图操作的时候会产生 快速失败
  transient int modCount;

  /**
     * The next size value at which to resize (capacity * load factor).
     *
     * @serial
     */
  //下一个值当大小改变的时候(容量 * 负载因子)
  int threshold;

  /**
     * The load factor for the hash table.
     *
     * @serial
     */
  //hashtable 的负载因子
  final float loadFactor;
  /**
     * Constructs an empty {@code HashMap} with the specified initial
     * capacity and load factor.
     *
     * @param  initialCapacity the initial capacity
     * @param  loadFactor      the load factor
     * @throws IllegalArgumentException if the initial capacity is negative
     *         or the load factor is nonpositive
     */
  //注释没什么翻译的，从这里能看出来，初始化的时候，容量最大为MAXIMUM_CAPACITY，不过话说Float.isNaN这个方法没看懂啊，先记下来，等等再看
  public HashMap(int initialCapacity, float loadFactor) {
    if (initialCapacity < 0)
      throw new IllegalArgumentException("Illegal initial capacity: " +
                                         initialCapacity);
    if (initialCapacity > MAXIMUM_CAPACITY)
      initialCapacity = MAXIMUM_CAPACITY;
    if (loadFactor <= 0 || Float.isNaN(loadFactor))
      throw new IllegalArgumentException("Illegal load factor: " +
                                         loadFactor);
    this.loadFactor = loadFactor;
    this.threshold = tableSizeFor(initialCapacity);
  }

  /**
     * Constructs an empty {@code HashMap} with the specified initial
     * capacity and the default load factor (0.75).
     *
     * @param  initialCapacity the initial capacity.
     * @throws IllegalArgumentException if the initial capacity is negative.
     */
  //也没什么翻译的，就是修改默认初始容量
  public HashMap(int initialCapacity) {
    this(initialCapacity, DEFAULT_LOAD_FACTOR);
  }

  /**
     * Constructs an empty {@code HashMap} with the default initial capacity
     * (16) and the default load factor (0.75).
     */
  //更加没什么翻译的
  public HashMap() {
    this.loadFactor = DEFAULT_LOAD_FACTOR; // all other fields defaulted
  }

  /**
     * Constructs a new {@code HashMap} with the same mappings as the
     * specified {@code Map}.  The {@code HashMap} is created with
     * default load factor (0.75) and an initial capacity sufficient to
     * hold the mappings in the specified {@code Map}.
     *
     * @param   m the map whose mappings are to be placed in this map
     * @throws  NullPointerException if the specified map is null
     */
  //也没啥翻译的，下面的方法看起来比较重要
  public HashMap(Map<? extends K, ? extends V> m) {
    this.loadFactor = DEFAULT_LOAD_FACTOR;
    putMapEntries(m, false);
  }
  /**
     * Implements Map.putAll and Map constructor.
     *
     * @param m the map
     * @param evict false when initially constructing this map, else
     * true (relayed to method afterNodeInsertion).
     */
  //实现了Map的putAll方法和构造器，由此可见，map的putAll方法也是用的这个putMapEntries方法，等等到下面的时候验证下
  final void putMapEntries(Map<? extends K, ? extends V> m, boolean evict) {
    int s = m.size();
    if (s > 0) {
      if (table == null) { // pre-size
        //size/loadFactor + 1，他为什么要ft命名啊，特么的。先解释一下，因为所hashmap的capacity = threshold * loadFactor  ，所以用 size/loadFactor+1 就能得到threshold
        float ft = ((float)s / loadFactor) + 1.0F;
        int t = ((ft < (float)MAXIMUM_CAPACITY) ?
                 (int)ft : MAXIMUM_CAPACITY);
        //这里会判断capacity是否大于threshold，大于的话，毫无一人，threshold扩容吧。。。
        if (t > threshold)
          threshold = tableSizeFor(t);
      }
      //和上面一样，不重复写了
      else if (s > threshold)
        resize();
      //capacity和threshold确定之后，通过put方法进行设置
      for (Map.Entry<? extends K, ? extends V> e : m.entrySet()) {
        K key = e.getKey();
        V value = e.getValue();
        putVal(hash(key), key, value, false, evict);
      }
    }
  }
  /**
     * Returns the value to which the specified key is mapped,
     * or {@code null} if this map contains no mapping for the key.
     *
     * <p>More formally, if this map contains a mapping from a key
     * {@code k} to a value {@code v} such that {@code (key==null ? k==null :
     * key.equals(k))}, then this method returns {@code v}; otherwise
     * it returns {@code null}.  (There can be at most one such mapping.)
     *
     * <p>A return value of {@code null} does not <i>necessarily</i>
     * indicate that the map contains no mapping for the key; it's also
     * possible that the map explicitly maps the key to {@code null}.
     * The {@link #containsKey containsKey} operation may be used to
     * distinguish these two cases.
     *
     * @see #put(Object, Object)
     */
  //map的重头方法，get和put，这里到了get了，来翻一下。
  //返回value当key被映射的时候，如果不包含则返回null。通常情况下，map的key可以为null，有则返回value，没有则返回null，这里最多返回一个映射(value)。
  //当这个map不包含这个key的时候，返回null并不是必须的情况，这个key通常有可能就是为null，然后containsKey操作可能在使用的时候返回两种情况。distinguish是啥意思。。。擦泪
  public V get(Object key) {
    Node<K,V> e;
    //重头戏就是这个getNode了，下面解释
    return (e = getNode(hash(key), key)) == null ? null : e.value;
  }

  /**
     * Implements Map.get and related methods.
     *
     * @param hash hash for key
     * @param key the key
     * @return the node, or null if none
     */
  //实现了map接口的get方法和relate方法
  final Node<K,V> getNode(int hash, Object key) {
    Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
    //hashmap是数组加链表的实现方式，所以说，现根据hashcode获取到第一个链表的节点，等于null的话说明没有呗，[(n - 1) & hash]得专门解读一下。
    if ((tab = table) != null && (n = tab.length) > 0 &&
        (first = tab[(n - 1) & hash]) != null) {
      //每次都检测第一个链表节点，为了下面的递归或者说while循环做准备
      if (first.hash == hash && // always check first node
          ((k = first.key) == key || (key != null && key.equals(k))))
        return first;
      //循环链表进行查找，找到到的话，就返回去，不然null呗
      if ((e = first.next) != null) {
        //链表在达到阈值(8)之后会转换为树节点，这个树是红黑树，树是怎么获取的，下面再写吧。
        if (first instanceof TreeNode)
          return ((TreeNode<K,V>)first).getTreeNode(hash, key);
        do {
          if (e.hash == hash &&
              ((k = e.key) == key || (key != null && key.equals(k))))
            return e;
        } while ((e = e.next) != null);
      }
    }
    return null;
  }

  /**
     * Returns {@code true} if this map contains a mapping for the
     * specified key.
     *
     * @param   key   The key whose presence in this map is to be tested
     * @return {@code true} if this map contains a mapping for the specified
     * key.
     */
  //不解释
  public boolean containsKey(Object key) {
    return getNode(hash(key), key) != null;
  }
  /**
     * Associates the specified value with the specified key in this map.
     * If the map previously contained a mapping for the key, the old
     * value is replaced.
     *
     * @param key key with which the specified value is to be associated
     * @param value value to be associated with the specified key
     * @return the previous value associated with {@code key}, or
     *         {@code null} if there was no mapping for {@code key}.
     *         (A {@code null} return can also indicate that the map
     *         previously associated {@code null} with {@code key}.)
     */
  //重头戏之二，put方法。
  //将key和value的关联放到这个map，如果key之前已经有值，将会被替换。
  public V put(K key, V value) {
    //难怪叫hashmap啊，都是需要根据hash进行操作的，所以，重写hashcode方法是多么的重要，对不对
    return putVal(hash(key), key, value, false, true);
  }

  /**
     * Implements Map.put and related methods.
     *
     * @param hash hash for key
     * @param key the key
     * @param value the value to put
     * @param onlyIfAbsent if true, don't change existing value
     * @param evict if false, the table is in creation mode.
     * @return previous value, or null if none
     */
  //实现了Map的put方法和related方法，related是啥意思，重新延迟？
  //onlyIfAbsent是ture的话，不改变已经存在的value，应该是会被某些包装方法使用
 //evict如果是false，这个table将会是创建模式
  final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                 boolean evict) {
    //n是用来记录hashmap大小的，tab是内部table，p是当前节点。i是当前hash对应数组下标
    Node<K,V>[] tab; Node<K,V> p; int n, i;
    if ((tab = table) == null || (n = tab.length) == 0)
      //如果这个hashmap初始化后还从未使用过，那么就用resize进行初始化操作
      n = (tab = resize()).length;
    //先根据hash看数组是否有值，没有值得话，直接将新节点放进去。
    if ((p = tab[i = (n - 1) & hash]) == null)
      tab[i] = newNode(hash, key, value, null);
    else {
      //如若不然，就开始链表(红黑树)的遍历，
      Node<K,V> e; K k;
      //会先看当前链表首个元素的key和即将放入的key是否相等，相等的话，就不进行后面的遍历了。直接对它进行处理。不然的话，需要整体遍历去看hash是否有对应的节点存储。
      if (p.hash == hash &&
          ((k = p.key) == key || (key != null && key.equals(k))))
        e = p;
      //当链表的元素的个数大于一定阈值的话，会转换为红黑树，所以，这里看节点是否已经变为了红黑树，如果已经变了，那么久按照红黑树的标准进行元素的插入，putTreeVal，后面看的时候在进行解释。
      else if (p instanceof TreeNode)
        e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
      else {
        //非红黑树，那就普通链表遍历
        for (int binCount = 0; ; ++binCount) {
          //因为说第一个元素已经进行过判断，所以直接next就可以。然后到了链表的最后一个位置，将val放到最后
          if ((e = p.next) == null) {
            p.next = newNode(hash, key, value, null);
            //这里会看链表中的值是否已经到了树化的阈值，到了之后，会将链表转换为红黑树。
            if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
              //treeifyBin放到下边说
              treeifyBin(tab, hash);
            break;
          }
          //如果key已经存在的话，那就直接break返回去。
          if (e.hash == hash &&
              ((k = e.key) == key || (key != null && key.equals(k))))
            break;
          p = e;
        }
      }
      //不为null的话，说明key已经存在，那么进行覆盖，新值覆盖，旧值返回。
      if (e != null) { // existing mapping for key
        V oldValue = e.value;
        //onlyIfAbsent猜测是为了某些工具类的方便而使用的，后面验证
        if (!onlyIfAbsent || oldValue == null)
          e.value = value;
        //linkedHashMap的回调，到时候再看吧
        afterNodeAccess(e);
        return oldValue;
      }
    }
    //put会触发modCount的变化。
    ++modCount;
    //size变化，如果超过阈值的话，那么hashmap进行扩容
    if (++size > threshold)
      resize();
    //linkedHashMap's callback ，后面说
    afterNodeInsertion(evict);
    return null;
  }
  /**
     * Initializes or doubles table size.  If null, allocates in
     * accord with initial capacity target held in field threshold.
     * Otherwise, because we are using power-of-two expansion, the
     * elements from each bin must either stay at same index, or move
     * with a power of two offset in the new table.
     *
     * @return the table
     */
  //用于初始化或者表空间扩张(双倍)，如果是null的话，分配的空间是initial capactiy * threshold，另外，因为我们使用的是两倍膨胀，table里的每个元素都必须待在同样的下表下，或者两倍位移在新的table中。
  final Node<K,V>[] resize() {
    Node<K,V>[] oldTab = table;
    int oldCap = (oldTab == null) ? 0 : oldTab.length;
    int oldThr = threshold;
    int newCap, newThr = 0;
    //如果table不为null
    if (oldCap > 0) {
      //如果容量已经达到了最大的话，那么，不进行扩容，直接return
      if (oldCap >= MAXIMUM_CAPACITY) {
        threshold = Integer.MAX_VALUE;
        return oldTab;
      }
      //如果newCapactiy < oldCap/2 && < max_cap && >= inital_cap，那么，表进行双倍扩充
      else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
               oldCap >= DEFAULT_INITIAL_CAPACITY)
        //double
        newThr = oldThr << 1; // double threshold
    }
    //threshold不为0的话，那么，capacity = threshold
    else if (oldThr > 0) // initial capacity was placed in threshold
      newCap = oldThr;
    else {               // zero initial threshold signifies using defaults
      //都没初始化，那就取默认值，capacity == 1 << 4 == 16，
      //threshold == 0.75f * 16 == 12
      //也就是说，阈值是总容量的3/4，也就是说，hashmap中，有至少四分之一的容量是被浪费了的
      newCap = DEFAULT_INITIAL_CAPACITY;
      newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
    }
    //如果只有capacity设置了，但是threshold没设置的话，会进入这里
    if (newThr == 0) {
      //这里其实结果还是12，ft的意思应该是float_threshold
      float ft = (float)newCap * loadFactor;
      //判断是否超出了最大值，然后阈值设置
      newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                (int)ft : Integer.MAX_VALUE);
    }
    // threshold setting
    threshold = newThr;
    @SuppressWarnings({"rawtypes","unchecked"})
    //设置新的node数组大小
    Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
    //然后将table指向新的table
    table = newTab;
    //如果hashmap没值得话，那就直接return，大小为初始化的值，如果不然，就进行数据迁移
    if (oldTab != null) {
      //循环数组
      for (int j = 0; j < oldCap; ++j) {
        Node<K,V> e;
        //如果数组的内容不为null的话，进行处理
        if ((e = oldTab[j]) != null) {
          //将oldTab数组对应的值设置为null，方便GC回收
          oldTab[j] = null;
          //进行链表首个节点的判断
          if (e.next == null)
            newTab[e.hash & (newCap - 1)] = e;
          else if (e instanceof TreeNode)
            //如果是treeNode的话，需要进行红黑树的扩容，至于这个split，后面碰到的话，再进行解释吧。
            ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
          else { // preserve order
            //还是挺反感这种命名方式的。写全点还是来的更直观一点。lo=low,hi=high。
            //low,high头尾节点，将单向链表变为双向链表。
            Node<K,V> loHead = null, loTail = null;
            Node<K,V> hiHead = null, hiTail = null;
            Node<K,V> next;
            //do-while进行链表的遍历操作
            do {
              next = e.next;
              //这个e.hash & oldCap是什么操作。。。
              //因为前面的hash方法没看，这里往下的东西，就看不懂了。。。
              //把hash方法看完回来了。e.hash是通过hash()方法获取到的值，在put的时候存放进去得，并不是说这个node本身的hashcode，这个还是得注意下。在另一篇文章里说了：hash & (length-1)得到的是该元素对应的数字下标值，至于为什么(length-1)，是因为length是2的倍数，转换为二进制的话就是0010,0100,1000,10000等，所以需要-1变成为0001,0011,0111,1111来方便进行&的操作。但是如果不-1的话，那么就是用最高位的1进行&的操作（oldCap就是tableLength），也就是说，e.hash & oldCap得到的值，如果hash是小于oldCap的话，那么一定是0，也就是说，随着oldCap的变化（2的倍数增长），e.hash & oldCap的值就不一定一直为0了，比如说，14 & 16，为0，17 & 16 为1，但是随着oldCap变化为32的时候，再次到了这里，14 & 31 == 0 ，但是17 & 31 就也等于0了。
              //如果等于0的话，那么该hash是值比较小的，那么用lowTail来存储
              if ((e.hash & oldCap) == 0) {
                if (loTail == null)
                  loHead = e;
                else
                  loTail.next = e;
                loTail = e;
              }
              //否则的话，说明hash>=oldCap，值相对来说大一点，所以用highTail来存储
              else {
                if (hiTail == null)
                  hiHead = e;
                else
                  hiTail.next = e;
                hiTail = e;
              }
              //hash值相对来说小点的node节点，用lowTail链表来存储，而大于oldCap的，用highTail存储，而且由于 hash & oldCap != 0 ，因此，在扩容之后，该节点并不在原先数组的下标中存放。而是进行等量平移
            } while ((e = next) != null);
            if (loTail != null) {
              loTail.next = null;
              newTab[j] = loHead;
            }
            if (hiTail != null) {
              hiTail.next = null;
              newTab[j + oldCap] = hiHead;
            }
            //上面这一段前移节点的代码，意思理解，但是为啥这么写，为啥用这种写法，还是不太理解。等自己实现一遍hashmap，应该说就能懂了吧。
          }
        }
      }
    }
    return newTab;
  }
  
  /**
     * Replaces all linked nodes in bin at index for given hash unless
     * table is too small, in which case resizes instead.
     */
  //根据得到的hash替换桶里的所有link node，除非表非常小，这种情况的话，用resize操作来代替
  //试一下windows是不是比较卡，好想确实比较卡啊
  final void treeifyBin(Node<K,V>[] tab, int hash) {
    int n, index; Node<K,V> e;
    //如果说tab的长度小于最小树化阈值的话，那么只改变大小，否则，进行树化
    if (tab == null || (n = tab.length) < MIN_TREEIFY_CAPACITY)
      resize();
    else if ((e = tab[index = (n - 1) & hash]) != null) {
      //首先根据hash获取到当前下边的元素
      //这个hd是Head ...的意思，tl 是tail ..的意思，能不用简写嘛
      TreeNode<K,V> hd = null, tl = null;
      do {
        //返回一个tree node ，也就是将链表的节点换为红黑树的节点
        TreeNode<K,V> p = replacementTreeNode(e, null);
        //然后进行关系的连接，虽然是tree node，但是这里，只是将单向链表转换为了双向链表
        if (tl == null)
          hd = p;
        else {
          p.prev = tl;
          tl.next = p;
        }
        tl = p;
      } while ((e = e.next) != null);
      //根据index获取到数组的元素，且设置为头结点，如果不为null的话，那么从头结点开始进行树化
      if ((tab[index] = hd) != null)
        //转换为红黑树
        hd.treeify(tab);
    }
  }
  /**
   * Forms tree of the nodes linked from this node.
   */
  final void treeify(Node<K,V>[] tab) {
    TreeNode<K,V> root = null;
    for (TreeNode<K,V> x = this, next; x != null; x = next) {
      next = (TreeNode<K,V>)x.next;
      x.left = x.right = null;
      //如果root节点尚未设置的话，那么先指向root节点
      if (root == null) {
        x.parent = null;
        x.red = false;
        root = x;
      }
      else {
        //然后根据hash值，进行左右节点的设置，然后进行红黑树的平衡
        K k = x.key;
        int h = x.hash;
        Class<?> kc = null;
        for (TreeNode<K,V> p = root;;) {
          int dir, ph;
          K pk = p.key;
          if ((ph = p.hash) > h)
            dir = -1;
          else if (ph < h)
            dir = 1;
          else if ((kc == null &&
                    (kc = comparableClassFor(k)) == null) ||
                   (dir = compareComparables(kc, k, pk)) == 0)
            dir = tieBreakOrder(k, pk);

          TreeNode<K,V> xp = p;
          if ((p = (dir <= 0) ? p.left : p.right) == null) {
            x.parent = xp;
            if (dir <= 0)
              xp.left = x;
            else
              xp.right = x;
            root = balanceInsertion(root, x);
            break;
          }
        }
      }
    }
    moveRootToFront(tab, root);
  }
```