---
title: 面试-RabbitMQ

categories: 

- 面试

tags: 

- 面试
date: 2020-12-16 14:55:25
description: 面试-RabbitMQ
---


1. 知道MQ么，MQ是什么，MQ的作用是什么？

    Message Queue，顾名思义，消息队列，基于生产者-消费者模式。作用可分为下面几点，但是MQ在单机环境下并不适用，更加适用于多系统，多服务的环境。

    - 异步处理，提高系统吞吐量
    - 应用解耦，不用关系其他系统怎么处理
    - 流量削峰，缓解短时间内的高并发请求，可通过控制mq长度来处理
    - 消息通讯，聊天室，点对点传输等
    - 日志处理，解决大量日志传输 
    - 详细回答......

2. MQ的缺点是什么？

    - 增加系统复杂性
    - 降低系统可用性
    - 数据一致性
    - 详细回答

3. RabbitMQ 和 其他MQ之间的区别有哪些？为什么选择RabbitMQ？

    - 虽然是Erlang开发，但是有很高的社区活跃度，可以保证可用性以及bug修复的及时性，国内外已经有很多公司使用rabbitMQ，并且有了很好的工程实践
    - 有良好的的web管理界面
    - 对于MQ没有特别强烈的定制需求，已有功能足以满足业务需求

4. RabbitMQ有哪些常见问题？如何避免这些问题？

    - 消息的乱序问题
    - 消息的重复发送问题

5. Rabbit的工作模式

    1. simple模式，最简单的收发模式
    2. 消费者监听模式（customer）
    3. work工作模式，信息被消费者竞争
    4. publish/subscribe 发布/订阅模式，每个消费者监听自己的队列
    5. routing 路由模式
    6. topic 模式

6. 如何保证信息消费时的幂等性？

7. 如何保证消息的可靠传输？

8. 消息队列满了怎么处理？过期失效怎么办？有消息积压怎么办？



