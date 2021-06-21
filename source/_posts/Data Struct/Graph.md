---
title: 图

categories: 

- 数据结构

tags: 

- 图

mathjax: true
date: 2019-04-16
description: 数据结构 图
---

1. 图的概念
   *  基本概念：图结构中，任意两个节点之间的关系是任意的，途中任意两个数据元素之间都可能相关。
   *  ADT：
     * 数据对象V：数据元素集合，顶点集；
     * 数据关系R：
       * R={VR}
       * VR={<v,w>| $$v,w \in V$$ 且P(v,w)，<v,w>表示从v到w的弧，P(v,w)定义了弧<v,w>的意义或者信息}
   *  顶点：图中的数据元素
   *  弧：<v,w>表示从v到w的一条弧;v是顶点的集合；vr是两个顶点之间关系的集合
     * 有向图
       * $$<v,w> \in VR$$，v为弧尾，w为弧头
     * 无向图
       * 若$$<v,w> \in VR$$，必有$$<w,v> \in VR$$，用(v,w)表示一条边
     * ![有向图和无向图图例](https://wenslo-blog.oss-cn-beijing.aliyuncs.com/Data%20Structures%20and%20Algorithms/Graph/01.png)
   *  定点数n和弧的数目e：
     * 无向图：$$0 \le e \le {\frac 1 2} n(n-1)$$
     * 有向图：$$0 \le e \le  n(n-1)$$
   *  完全图：有n(n-1)/2条边的无向图
   *  有向完全图：有n(n-1)条弧的有向图
   *  稀疏图：边或弧很少的图
   *  稠密图：边或弧较多的图
   *  子图：$$G=(V,{E})，G1=(V1,{E1})$$，若$$V1 \subseteq V$$，且$$E1 \subseteq E$$，则称G1为G的子图
   *  邻接点：无向图中，$$(V,V1) \in E$$，则v,v1互为邻接点
   *  顶点v的度：与v相关联的边的数目，TD(v)
   *  有向图中，若<v,v1> \in A，则顶点v邻接到顶点v1，而顶点v1邻接自v
     * 出度：以v为尾的弧的数目，OD(v)
     * 入度：以v为头的弧的数目，ID(v)
     * TD(v) = OD(v) + ID(v)
   *  路径
     * 无向图中顶点v到v1的路径：一个顶点序列
     * 有向图中，路径也是有向的
     * 回路
     * 简单路径：顶点序列中顶点不重复的路径
   *  连通图、连通分量、强连通图、强连通分量
     * 无向图中：顶点v到顶点v1有路径，则v和v1是连通的
     * 如果图G中任意两个点都是连通的，则图G就是连通图
     * 连通分量：无向图中的极大连通子图
     * 有向图中：若任意的顶点vi到vj都有路径，则为强连通图
     * 强连通分量：有向图中的极大连通子图
   *  一个连通图的生成树：一个极小连通子图，含有途中全部节点，但只有足以构成一棵树的n-1条边。
     * 一棵有n个顶点的生成树有且仅有n-1条边
     * 但有n-1条变得图不一定是生成树
   *  有向图
     * 如果有一个顶点的入度为0，其余顶点的入度都为1，则是一个有向树。
2. 图的存储结构
   * 数组表示：两个数组分别存放顶点信息和弧信息
     * 代码地址：https://github.com/wenslo/data_struct_java/blob/master/src/main/java/com/github/wenslo/ds/graph/ArrayGraph.java
   * 链式结构：为途中没一个顶点创建一个单链表，其中的节点表示与依附于顶点的边
     * 链表存储
     * 十字链表——有向图
     * 邻接多重表——无向图
   * 遍历方式
     * 深度优先
     * 广度优先
     * 比较简单，类似于树的先序遍历和中序遍历
