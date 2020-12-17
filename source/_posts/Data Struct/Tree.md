---
title: 树

categories: 

- 数据结构

tags: 

- 树

mathjax: true
date: 2019-04-12
---

**树的定义**：

* 一棵树是一些节点的集合。这个集合可以是空集；若的不是空集，则树由称作**根（root）**的节点$$r$$以及0个或者多个非空的(子)树$$T_0，T_1…..，T_k$$组成，这些子树中每一棵的根都被来自根$$r$$的一条有向的**边（edge）**所连结。
* 一棵树是$$N$$个结点和$$N-1$$条边的集合，其中的一个节点叫做根。
* 没有儿子的结点成为**树叶（leaf）**，叶节点。
* 具有相同父亲的节点为**兄弟（siblings）**节点
* 从节点$$n_1$$到$$n_k$$的**路径（path）**定义为节点$$n_1，n_2，……，n_k$$的一个序列，使得对于$$1\le i \lt k$$的节点$$n_i$$是$$n_i+1$$的父亲。这条路径的**长（length）**是位该路径上的边的条数，即$$k-1$$。从每一个节点到它自己都有一条边长为0的路径。注意，在一棵树中从根到每个节点恰好存在一条路径。
* 对任一节点$$n_i，n_i$$的**深度（depth）**为从根到$$n_i$$的唯一的路径的长。因此，根的深度为0。$$n_i$$的**高（height）**是从$$n_i$$到一片树叶的最长路径的长。因此所有的树叶的高都是0。一棵树的高等于它的根的高。一棵树的深度等于它的最深的树叶的深度；该深度总是等于这棵树的高。
* 如果存在从$$n_1$$到$$n_2$$的一条路径，那么$$n_1$$是$$n_2$$的一位**祖先（ancestor）**而$$n_2$$是$$n_1$$的一个**后裔（descendant）**。如果$$n_1 \neq n_2$$，那么$$n_1$$是$$n_2$$的**真祖先（proper ancestor）**而$$n_2$$是$$n_1$$的**真后裔（proper descendant）**。

**满二叉树**：一棵深度为k且有2^k-1个节点的二叉树

**完全二叉树**：深度为k有n个节点的二叉树，并且仅当其每一个节点都与深度为k的满二叉树中编号从1至n的节点一一对应时，成为完全二叉树。

![树图](https://wenslo-blog.oss-cn-beijing.aliyuncs.com/Data%20Structures%20and%20Algorithms/Tree/01.png)

**二叉树的性质**：

* 在二叉树的第i层上至多有$$2^i+1 个$$结点($$i>=1$$)
* 深度为k的二叉树至多有$$2^k -1$$个结点($$k>=1$$)
* 对任何一棵二叉树T，如果其终端节点数为$$n_0$$，度为2的结点数为$$n_2$$，则$$n_0=n_2+1$$
* 具有n个结点的完全二叉树的深度为$$(log_2n) + 1$$
* 如果有一棵有n个结点的完全二叉树(其深度为$$(log_2n)+1$$层，每层从左到右)，则对任一结点i($$1 \le i \le n$$)，有：
  * 如果i=1，则节点i是二叉树的根，无双亲；若i>1，则其parent(i)是节点 i/2
  * 如果2i>n，则节点i无左孩子(节点i为叶子节点)；否则其做孩子leftChild(i)是节点2i
  * 如果2i+1>n，则节点i无右孩子；否则其有孩子rightChild(i)是节点2i+1

二叉树的**顺序存储**：

* 就是将完全二叉树上编号为i的节点元素存储在数组对应的下标中。
* 利用二叉树的最后一条性质，可以快速定位到二叉树的左右子节点和父节点。
* 示例代码地址：https://github.com/wenslo/data_struct_java/blob/master/src/main/java/com/github/wenslo/ds/tree/ArrayBinTree.java

树的**遍历方式**（模板为上图的完全二叉树）：

* 先序（根）遍历
  * 对节点的处理工作是在它的诸儿子节点被处理之前进行的
  * 遍历结果为 1   2   4   8   9   2   10   11   3   6   12   13   7   14   15 
* 中序（根）遍历
  * 中序遍历左子树，然后访问根节点，然后中序遍历右子树
  * 遍历结果为 8   4   9   2   10   2   11   1   12   6   13   3   14   7   15
* 后序（根）遍历
  * 一个结点初的工作是在它的诸儿子节点被计算后进行的
  * 遍历结果为  8   9   4   10   11   2   2   12   13   6   14   15   7   3   1 
* 示例代码地址：https://github.com/wenslo/data_struct_java/blob/master/src/main/java/com/github/wenslo/ds/tree/LinkedBinTree.java

**线索二叉树**：

* 对二叉树进行中序排序，将所有的节点右子节点为空的指针指向它的后继节点，将所有的节点左子节点为空的指针指向它的前驱节点。如下图所示

* ![线索二叉树](https://wenslo-blog.oss-cn-beijing.aliyuncs.com/Data%20Structures%20and%20Algorithms/Tree/02.png)

树的**存储结构**：

* 双亲表示法：除了根节点之外，每个节点都会指向其双亲
* 孩子表示法：每个节点，如果有子节点，则都会指向其子节点
* **孩子兄弟表示法：两个链域，左链域指向其第一个子节点，右链域指向其第一个兄弟节点**
* ![树表示法图例](https://wenslo-blog.oss-cn-beijing.aliyuncs.com/Data%20Structures%20and%20Algorithms/Tree/03.png)

森林：就是好多棵树

森林/树与二叉树的互相转换：

* 森林转换为二叉树
  * $$F={T_1,T_2……T_m}$$是森林，转为二叉树$$B=(root,LB,RB)$$
  * 若$$F$$为空，则m=0，B为空
  * 若$$F$$非空，则B的根root是森林中的第一棵树的根$$ROOT(T_1)$$；B的左子树LB是从$$T_1$$中根节点的子树森林$$F1={T_1,T_2……T_m}$$转换而成的二叉树；B的右子树RB是从森林$$F2={T_1,T_2……T_m}$$转换而成的二叉树 

* ![森林，树，二叉树相互转换图](https://wenslo-blog.oss-cn-beijing.aliyuncs.com/Data%20Structures%20and%20Algorithms/Tree/04.png)

最优二叉树(哈弗曼树)：

* **概念：带权路径长度最短的树**
* **路径长度：结点之间的树枝的总数**
* **树的路径长度：从根到每一结点的路径长度之和**
* **树的带权路径长度：叶子结点的带权路径长度之和。设有** **n** **片叶子，它们的权值分别为$$w_1,w_2,…w_n$$相应的路径长度分别为$$l_1,l_2,…,l_n$$**，则树的带权路径长度（Weight Path Length）可记为：$$WPL=\sum_{k-1}^n w_k l_k$$
