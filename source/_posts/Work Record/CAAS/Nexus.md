---
title: Nexus打包碰到的问题

categories: 

- 工作日常记录

tags: 

- 工作
- 农科院
date: 2019-11-01
description:  Nexus打包碰到的问题
---

打包如果打的是snapshot的包的话，包里只包含当前project下的文件，其他的依赖获取不到。

比如说，A依赖B，B依赖C，B打的是snapshot包，但是A想使用C的东西，获取不到，查看依赖，也看不到C。

两个解决办法：

1. 打release包，这样A依赖B的release版本，就能够获取到想要的东西
2. 打fat-jar，但是这样，包的体积会非常大