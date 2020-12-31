---
title: 串的模式匹配算法

categories: 

- 数据结构

tags: 

- 算法
date: 2019-04-09
description: 字符串匹配
---

> 面试常见题

&emsp;&emsp;给定一个串，然后给其一个子串，获取**子串在串中的下标**，LeetCode也收录了这个问题。

&emsp;&emsp;解法一：贪婪法

&emsp;&emsp;假设有两个串，主串A，子串B。首先说循环A，然后循环B，判断子串中的值和主串中的是否完全一致，如果不一致的话，A开始下一次循环，继续这个过程。最欢情况的话，就是O(m*n)。这个是最容易想到的，也是最简单的一个，同时也是最慢的一个。

&emsp;&emsp;解法二：KMP算法

&emsp;&emsp;如下图所示，如果T[2] != P[2]，按照贪婪法，应该是T移动到1，P移动到0，然后重新开始匹配。而KMP算法的目的，就是说跳过无用的匹配，使其无用操作变少。

![KMP示例](https://wenslo-blog.oss-cn-beijing.aliyuncs.com/Data%20Structures%20and%20Algorithms/StringMatcher/01.jpg)

&emsp;&emsp;KMP算法的核心，就是next数组，需要通过next数组，来获取其移位的长度。***next[i]，i从1开始计算，则表示，除去第i个数，在一个字符串里面从第一个数到第(i-1)字符串前缀和后缀最长重复的个数***。而这个个数，就是子串移位的长度。

&emsp;&emsp;公式(图片来源于网络)如下

![next函数公式](https://wenslo-blog.oss-cn-beijing.aliyuncs.com/Data%20Structures%20and%20Algorithms/StringMatcher/02.png)

&emsp;&emsp;举例说明前缀和后缀，在"abcd"中，前缀为"abc"，后缀为"bcd"。

&emsp;&emsp;举例模式串为：**ABAAAABC**，用大写看起来舒服点。

&emsp;&emsp;子串说明：

| 各个子串 | 前缀                                                        | 后缀                                                         | 最大公共元素长度 |
| -------- | ----------------------------------------------------------- | ------------------------------------------------------------ | ---------------- |
| A        | 无                                                          | 无                                                           | 0                |
| AB       | A                                                           | B                                                            | 0                |
| ABA      | <font color=red>A</font>；AB                                | <font color=red>A</font>；BA                                 | 1                |
| ABAA     | <font color=red>A</font>；AB；ABA                           | <font color=red>A</font>；AA；BAA                            | 1                |
| ABAAA    | <font color=red>A</font>；AB；ABA；<br/>ABAA                | <font color=red>A</font>；AA；AAA；<br/>BAAA                 | 1                |
| ABAAAA   | <font color=red>A</font>；AB；ABA；<br>ABAA；ABAAA          | <font color=red>A</font>；AA；AAA；<br/>AAAA；BAAAA          | 1                |
| ABAAAAB  | A；<font color=red>AB</font>；ABA；<br/>ABAA；ABAAA；ABAAAA | B；<font color=red>AB</font>；AAB；<br/>AAAB；AAAAB；<br/>BAAAAB | 2                |

&emsp;&emsp;用一张图来表示next函数的执行过程：

![next函数执行过程](https://wenslo-blog.oss-cn-beijing.aliyuncs.com/Data%20Structures%20and%20Algorithms/StringMatcher/03.png)

接下来简述一下next函数的执行过程，我们用P来代表上面的子串。

1. 第一步，i=j=0，第一步没有可比较对象，所以**next[0]=0，i++**。

2. 第二步，i=1，j=0。P[i]!=P[j]。next[1]=0，i++**。
3. 第三步，i=2，j=0。P[i]==P[j]。next[2]=next[j]+1=1，i++，j++**。
4. 第四步，i=3，j=1。P[i]!=P[j]。j = next[j-1]，再次进行对比，P[i]==P[j-1]，j = j-1+1，i++。

5. 第五步，同上
6. 第六步，同上
7. 第七步，同上
8. 第八步，同上

next函数的代码如下，写的并不严谨，只表示主要思路，仅供参考：

```java
   /**
     * 获取滑动位的算法，这个算法思路很简单。就是对比前缀和后缀，看其中重复的子串的长度，则就是next[]函数的值
     * 但是要注意的是，前缀必须从头开始算，后缀必须从最后一个数开始算
     */
    public void next(String expression, int[] next) {
        int i = 0, j = 0;
        //第一个已经固定死为0了。
        while (i < expression.length()) {
            if (i == 0) {
                next[i] = 0;
                i++;
                continue;
            }
            //如果相等的话
            if (Objects.equals(expression.charAt(i), expression.charAt(j))) {
                System.out.println("expression.charAt(i) is " + expression.charAt(i) + "  expression.charAt(j) is " + expression.charAt(j));
                next[i] = j + 1;
                j++;
            } else {
                while (j != 0) {
                    j = next[j - 1];
                    System.out.println("expression.charAt(i) is " + expression.charAt(i) + "  expression.charAt(j) is " + expression.charAt(j));
                    if (Objects.equals(expression.charAt(i), expression.charAt(j))) {
                        next[i] = j + 1;
                        j++;
                        break;
                    }
                }
            }
            System.out.println("i --------> " + i + " j------------->" + j + "  next[" + i + "] = " + next[i]);
            i++;
        }

        for (int k = 0; k < next.length; k++) {
            System.out.print(next[k] + "  ");
        }
        System.out.println();
    }
```

&emsp;&emsp;至此，next数组就已经全部获取到了。接下来就是，利用这个next数组，来查询子串是否存在并且返回下标。如下图所示：

![next数组使用](https://wenslo-blog.oss-cn-beijing.aliyuncs.com/Data%20Structures%20and%20Algorithms/StringMatcher/04.png)

执行过程描述：

1. 第一次执行，匹配到S[i] != P[j]的时候，从next数组中获取其前一位的值（next[j-1] = 0）

   于是能够知道，下一次对比，是从P[0]开始，OK。j=0，i++。

2. 第二次执行，匹配到S[i] != P[j]的时候，从next数组中获取其前一位的值（next[j-1] = 2）

   于是能够知道，下一次对比，是从P[2]开始，OK。j=2，i=i+k。

3. 第三次执行。全部匹配，OK，退出，返回下标。

示例代码如下：

```java
public int solution(String common, String expression) {
        char[] commonCharArray = common.toCharArray();
        char[] expressionCharArray = expression.toCharArray();
        int[] next = new int[expression.length()];
        next(expression, next);
        int commonLength = common.length();
        int patternLength = expression.length();
        int i = 0, k = 0;
        while (i < commonLength) {
            int incrementValue = 0;
            while (k < patternLength) {
                if (Objects.equals(commonCharArray[i + incrementValue], expressionCharArray[k])) {
                    if (k == patternLength - 1) {
                        return i - (k - incrementValue);
                    } else {
                        k++;
                        incrementValue++;
                    }
                } else {
                    i = k + i;
                    if (k != 0) {
                        k = next[k - 1];
                    } else {
                        i++;
                    }
                    break;
                }
            }
        }
        return -1;
    }
```

