---
title: 动态规划，lintcode 116 Jump Game

categories: 

- 动态规划

tags: 

- lintcode

mathjax: true
date: 2021-01-13 15:56:17
description: 动态规划
---

#### 题目如下

给出一个非负整数数组，你最初定位在数组的第一个位置。　　　

数组中的每个元素代表你在那个位置可以跳跃的最大长度。　　　　

判断你是否能到达数组的最后一个位置。

##### 先确定最后一步

最后一步为n-1
那么前一步也就是i < n- 1
那么最后一步的最大距离为：$n - 1 -i <= a_i$

##### 拆分子问题

那么子问题就变为 f[j]表示能不能到达数组j


##### 转移方程

$$ f[j] = OR_{ 0 <= i< j }(f[i] AND i + a[i] >= j) $$

##### 确定初始条件和边界

初始条件：f[0] = ture

##### 确定计算顺序

从左到右，从小到大

##### 代码如下

```java
    public boolean canJump(int[] A) {
        int n = A.length;
        boolean f[] = new boolean[n];
        f[0] = true;
        for (int j = 1; j < n; j++) {
            f[j] = false;
            for (int i = 0; i < j; i++) {
                if (f[i] && i + A[i] >= j) {
                    f[j] = true;
                    break;
                }
            }

        }
        return f[n - 1];
    }
```