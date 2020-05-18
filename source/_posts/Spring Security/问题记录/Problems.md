---
title: Spring Security碰到的一些问题

categories: 

- Spring Security

tags: 

- Security
date: 2020-05-18 11:36:08

---



2020-05-18 碰到的问题

添加了@PreAuthorize注解后，访问某些方法，在这些方法体内，对象无法自动注入，因此会直接抛出NullPointerException，经查，使用该注解的方法，必须为public，否则，无法自动注入。

原因为，SpringSecurity使用AOP来进行包装，而AOP，只能对公共方法进行运行时修改。