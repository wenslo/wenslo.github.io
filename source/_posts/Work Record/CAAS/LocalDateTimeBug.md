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

If you want to take complete control of Spring MVC, you can add your own `@Configuration` annotated with `@EnableWebMvc`, or alternatively add your own `@Configuration`-annotated `DelegatingWebMvcConfiguration` as described in the Javadoc of `@EnableWebMvc`.

上面是Spring 官方文档的说明。

如果使用了@EnableMvc，会全面接管配置，导致默认配置失效。

最好是自己写个类，实现WebMvcConfig，然后自定义实现相关接口。

默认的Jackson的ObjectMapper，也有自己的AutoConfig，默认不用改，如果自定ObjectMapper的话，需要注意，@Transient注解标识的字段，默认是不会处理的。最好是继承之前的ObjectMapper，然后定义。不然的话，可能会有问题。