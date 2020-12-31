---
title: MySQL查询性能剖析

categories: 

- MySQL

tags: 

- MySQL
date: 2020-12-16 16:41:31

description: MySQL查询性能剖析
---

首先，也是最重要的一点，需要记录慢查询日志，然后针对日志，进行分析。假设已经定位到问题是查询语句的问题，如果不是，需要自顶而下进行考虑，外部接口慢？文件解析慢？正则滥用？还是暴力搜索算法等。

剖析工具

| Profile分析

profile分析最重要的还是在语句执行期间剖析服务器的具体操作，当一条查询到了数据库后，会记录信息到一张临时表，并且给一个整数的id作为标识符，方便查询。

```sql
set profiling = 1;
select * from nicer_but_slower_film_list;

show profiles ;
# show prifiles 会显示出刚刚那条查询语句的查询id，获取到后
show profile for query id;
# 会打印出这条查询的详细信息，由于打印出来的信息不太直观，可以优化一下，如下

set @query_id = 1;
select state,
       sum(duration)            as total_r,
       round(100 * sum(duration) /
             (select sum(duration) from information_schema.PROFILING where QUERY_ID = @query_id), 2
           )                    as pct_r,
       count(*)                 as calls,
       sum(duration) / count(*) as "r/call"
from information_schema.PROFILING
where QUERY_ID = @query_id
group by state
order by total_r desc;

```

这个工具能告诉我们哪些活动花费了很长时间，但是并不会告诉我们为什么，还是需要我们具体情况具体分析。

| show status  || show global status

这个工具只是一个计数器，但是并不是剖析工具，用来显示一些活动的频繁程度，但是无法给出消耗了多少时间。只有一条信息显示的是操作时间(Innodb_row_lock_time)，而且是全局的，无法测量会话级别。

虽然无法知道哪些操作耗时最长，但是可以猜测。最有用的句柄计数器(handle counter)，临时文件和表计数器。示例如下：

```sql
flush status ;
select * from nicer_but_slower_film_list;
show status where Variable_name like 'Handler%' or Variable_name like 'Created%';
```

从执行结果中可以看到，使用了三张临时表，其中两个磁盘临时表，而且有很多没有用到索引的读操作(Handler_read_rnd_next)，这个查询有可能是使用了join多表关联查询，但是没有合适的索引，或者是一个子查询创建了临时表，并且和其他表做联合查询，而用于保存子查询结果的临时表没有索引等。

show status 可以类比为explain，它们两者可以得到相似的结果，但是explain是预估的结果，而show status是精确测量的结果，比如explain无法告诉你临时表是否是磁盘表还是内存临时表。

| performance_schema

performance_schema无法提供查询阶段的细节和计时信息，但是，能够知道其他很多有意思的信息，比如：user_variables_by_thread, global status,global_variables,session_variables等，可以用排错或者定位问题使用

