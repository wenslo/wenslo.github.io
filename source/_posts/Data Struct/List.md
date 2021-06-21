---
title: 数据结构-线性表

categories: 

- 数据结构

tags: 

- 线性表

date: 2019-04-06
description: 线性表
---



&emsp;&emsp;线性表分大致两种实现：**顺序实现**，**链表实现**。

&emsp;&emsp;对于顺序实现来说，特点有这么几个：1、**基于数组**，2、**Locate是常数时间O(1)**，3、**插入和删除要移动大量元素，最坏情况为O(N)，平均情况为线性时间，最好情况是O(1)**

&emsp;&emsp;对于链表实现来说，特点是这么几个：1、**元素不连续存储**，2、**Locate是线性时间**，3、**插入、删除开销小**。

&emsp;&emsp;链表分类有：**单向链表**，**循环链表**，**双向链表**，**双向循环链表**。单向链表的话，只有一个next指针指向下一个元素；循环链表的话，tail.next->head；双向链表的话，一个next指针，一个prev指针；双向循环链表的话，head->tail，tail->head;

&emsp;&emsp;对于顺序实现的插入和删除，实现步骤如下：**插入的时候**，需要看其原先的位置有没有元素，如果没有，则直接赋值即可。但是如果有值的话，则需要将该位置之后的元素通通向后后移一位。**删除的时候**同理，删除了元素之后，该元素之后的数据，需要通通向前移动一位。

&emsp;&emsp;对于链式存储的插入和删除，实现步骤如下：**插入的时候**，看位置有没有元素，没有，直接赋值进去，同时看有没有上一个元素，有的话，prev指向上一个元素，否则，自己作为头结点。如果有元素，麻烦一点，首先得看这个元素是不是头节点，是头结点，那插入之后，自己的next需要指向之前的头结点。不是头结点的话，自己的next指到上一个元素的next，那上一个元素的next指向自己，自己的prev指到上一个元素。**删除的时候**，和插入的判断差不多，不想写了，意思差求不多。

&emsp;&emsp;用list实现多项式的相加，也就是**合并多项式**，写一下思路吧，代码不写了，和归并差不太多，多了一层判断，也就是比较大小的时候，需要对比多项式**指数部分**是否相等，相等的话，合并多项式，就完了。多项式相减也是同理。

&emsp;&emsp;写LinkedList的时候，参考了一下jdk的源码，发现个有意思的事情。在jdk中，getElement的时候，做了一层优化，**根据下标，判断是否小于size/2，小于的话，则从前开始遍历，否则，从后往前开始遍历。**这样的话，减少了不必要的遍历。而且，获取node的时候，这通常第一就会想到递归。但是，jdk并不是用递归进行的处理，而是用的循环，这样性能上相对于递归来说更好。代码如下：

```java
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
```

&emsp;&emsp;又发现个有意思的事情，jdk的LinkedList，是**双链表**，记录有头尾节点，因此，添加元素直接对尾节点进行操作就可以，速度很快。而C语言描述中，是**单链表**，则添加为O(n)。先往后看看，可能刚开始只是用单链表演示而已。