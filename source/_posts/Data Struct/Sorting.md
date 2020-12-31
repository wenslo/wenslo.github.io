---
title: 排序

categories: 

- 数据结构

tags: 

- 排序

mathjax: true
date: 2019-04-18
description: 排序
---

插入排序：

* 直接插入
* 折半插入
* 2路插入
* 表插入
* 上面其实就是查找方式不同而已，操作还是一致的
* ![插入排序图解](https://wenslo-blog.oss-cn-beijing.aliyuncs.com/Data%20Structures%20and%20Algorithms/Sort/InsertSort01.png)
* 示例代码地址：https://github.com/wenslo/data_struct_java/blob/master/src/main/java/com/github/wenslo/ds/sorting/InsertSorting.java

冒泡排序：

* 类似于冒泡泡，每次将最大值浮到表面，浮动完毕之后，排序结束
* ![冒泡排序图解](https://wenslo-blog.oss-cn-beijing.aliyuncs.com/Data%20Structures%20and%20Algorithms/Sort/BumbleSort01.png)
* 示例代码地址：https://github.com/wenslo/data_struct_java/blob/master/src/main/java/com/github/wenslo/ds/sorting/BumbleSorting.java

快速排序：

1. 寻找一个基准点（pointer），进行循环，将比它小的放其左边，将比其大的放右边
2. 然后对左右两个数组重复1的过程，最终结果就为排序后的结果
3. 数组越大，速度越快。因为最快情况：基准点左侧只有一个元素，而剩下的元素全在基准点右侧，而去随机数的话，假设10W个元素，最坏情况的可能性为十万分之一。
4. ![算法图解](https://wenslo-blog.oss-cn-beijing.aliyuncs.com/Data%20Structures%20and%20Algorithms/Sort/QuickSort01.png)
5. 示例代码地址：https://github.com/wenslo/data_struct_java/blob/master/src/main/java/com/github/wenslo/ds/sorting/QuicklySorting.java

希尔排序：

* 缩小增量排序
* ![希尔排序图解](https://wenslo-blog.oss-cn-beijing.aliyuncs.com/Data%20Structures%20and%20Algorithms/Sort/ShellSorting01.png)
* 示例代码地址：https://github.com/wenslo/data_struct_java/blob/master/src/main/java/com/github/wenslo/ds/sorting/ShellSorting.java

选择排序：

* 类似于冒泡排序，不过每次讲最小值放入已排序的数组
* ![选择排序图解](https://wenslo-blog.oss-cn-beijing.aliyuncs.com/Data%20Structures%20and%20Algorithms/Sort/SelectSort01.png)
* 示例代码地址：https://github.com/wenslo/data_struct_java/blob/master/src/main/java/com/github/wenslo/ds/sorting/SelectSorting.java

堆排序：

* 构建一个堆，然后首尾互换，除了尾节点之外，再次构建堆，这就是堆排序的过程

* 堆排序，最主要的就是构建一个堆，而构建堆，需要知道最后一个非叶节点的位置。
  * 假设节点数目为n，最后一个非叶节点为i。
  * 分两种情况
    * 由完全二叉树的性质可知：i的左子节点为2i+1，i的右子节点为2i+2
    * 只有左子节点，那么该节点下标为n-1，那么，2i+1=n-1，可以推出i=n/2 -1 
    * 有左/右子节点，那么左子节点下标则为n-2，右子节点下标为n-1，2i+2=n-1，i= (n-3)/2=n/2-3/2。由于二叉树最后一个节点为左孩子，那么该二叉树的节点数为偶数，反之，则为奇数。再因为java中，除不尽则向下取整，因此，i=n/2-1
  * i=n/2-1得证
* ![堆排序图解](https://wenslo-blog.oss-cn-beijing.aliyuncs.com/Data%20Structures%20and%20Algorithms/Sort/HeapSort01.png)
* 示例代码地址：https://github.com/wenslo/data_struct_java/blob/master/src/main/java/com/github/wenslo/ds/sorting/HeapSorting.java

归并排序：

* 采用分而治之的思想（divide-and-conquer），拆分成最小单元进行排序后，一步一步进行合并
* ![归并排序图解](https://wenslo-blog.oss-cn-beijing.aliyuncs.com/Data%20Structures%20and%20Algorithms/Sort/MergeSort01.png)
* 示例代码地址：https://github.com/wenslo/data_struct_java/blob/master/src/main/java/com/github/wenslo/ds/sorting/MergeSorting.java

基数排序：

* 也比较简单，个位开始，依次开始对比，放入对应的bucket中
* 然后十位，百位，以此类推，到了某一位，所有数字都没有的话，排序结束，输出结果则为排序后的内容
* ![基数排序图解](https://wenslo-blog.oss-cn-beijing.aliyuncs.com/Data%20Structures%20and%20Algorithms/Sort/RadixSort01.png)
* 示例代码地址：https://github.com/wenslo/data_struct_java/blob/master/src/main/java/com/github/wenslo/ds/sorting/RadixSorting.java

桶排序：

* 和上面基数排序很像，不过桶排序的**映射函数**为$$f=array[i]/k，k^2=n$$。n为所有元素个数
* 然后设置一个定量的桶，将元素放到对应的桶里，然后对每个非空的桶进行排序（插入，选择等）
* ![桶排序图解](https://wenslo-blog.oss-cn-beijing.aliyuncs.com/Data%20Structures%20and%20Algorithms/Sort/BucketSort01.png)
* 示例代码地址：https://github.com/wenslo/data_struct_java/blob/master/src/main/java/com/github/wenslo/ds/sorting/BucketSorting.java

计数排序：

* 最快的排序，类似于桶排序和基数排序。
* 给每个元素一个桶
* ![计数排序图解](https://wenslo-blog.oss-cn-beijing.aliyuncs.com/Data%20Structures%20and%20Algorithms/Sort/CountingSort01.png)
* 示例代码地址：https://github.com/wenslo/data_struct_java/blob/master/src/main/java/com/github/wenslo/ds/sorting/CountSorting.java