---
title: 中文数据中筛选出英文数据

categories: 

- 工作日常记录

tags: 

- 工作
- 农科院
date: 2019-10-19
description:  中文数据中筛选出英文数据
---

数据库表中，有字段为***title***，只存中文，但是英文数据混入，需要筛选出来将其放到英文库中。如何找出来，就成为了一个问题。

其实很简单，**select id from xxx x where length(x.title) != char_length(title)**

因为说，MySQL在UTF-8的编码下，一个汉字字符占据三个字节，但是英文是一个字符一个字节，这就会导致说，如果title为英文，那么他的字符数和字节数是肯定能对上的，中文反之。

延伸一下，MySQL的varchar类型，并不是按照字节来算，而是字符，比如说，定义了一个varchar(10)，存“西门吹雪”是没有任何问题的，占用会是4，存"Jobs"也一样。MySQL对此作了优化。

再延伸一下，UTF-8一个汉字占据两三个字节，GBK一个汉字两个字节，部分汉字占据四个字节