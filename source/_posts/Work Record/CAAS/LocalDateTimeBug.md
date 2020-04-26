---
title: LocalDateTime的BUG

categories: 

- 工作日常记录

tags: 

date: 2020-04-22
---

记录一个BUG

SpringBoot Version : 2.1.0 Release

使用ResponseBody返回json时，如果有LocalDateTime等类型，返回会一直是数组类型，不是想要的结果，移除掉@EnableWebMvc就可以了，不知道为啥，等等Google下原因。

百度还是不靠谱。

查了下，在2以后的版本中，@EnableWebMvc会产生冲突。。。

