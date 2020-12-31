---
title: Spring Data JPA EntityManager的一个问题

categories: 

- Spring Data JPA

tags: 

- Spring Data JPA
date: 2020-05-28 20:35:09

description: EntityManager的一个问题
---



JPA实体生命周期有四种状态

1. New：瞬时对象，尚未有id，还未和Persistence Context建立关联的对象。

2. Managed：持久化受管对象，有id值，已经和Persistence Context建立了关联的对象。

3. Datached：游离态离线对象，有id值，但没有和Persistence Context建立关联的对象。

4. Removed：删除的对象，有id值，尚且和Persistence Context有关联，但是已经准备好从数据库中删除

由于JPAQuery绑定EntityManager，查询出来的实体还有ID，实际上是处于Managed状态，查询完毕之后，每一条都会执行一条update语句来更新数据库，比较浪费资源，如果不想这么做的话，可以使用new JPAQuery，而不是JPAQueryFactory，查询完毕，手动执行entityManager.clear()来讲对象改为Detached状态，这样，便不会每一条都执行一遍update操作。