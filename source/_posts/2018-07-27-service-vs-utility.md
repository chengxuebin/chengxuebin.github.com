---
layout: post
title: "Service 和 Utility 区分"
category: "spring"
tags: ['java', 'spring']
---

开发者有时会纠结一部分复用的逻辑是实现在 Service 中，还是 Utility 或 Helper 中，下面讨论一下他们的区别。

### Service

* 包含业务逻辑
* 应用上下文内使用

**举例**

TicketService 购票服务

* buyTicket 买票
* refundTicket 退票

业务逻辑可能涉及应用内的用户、订单、短信、邮件、日志等

<!-- more -->

### Helper 或 Utility

* 可复用的组件
* 通用，不限制在应用内

**举例**

MoneyUtil/MoneyHelper  账务助手

* exchange(from, to) 汇率兑换
* calcultateTax 计算税金

不和应用上下文相关，甚至可以在其它应用使用。


### 参考：

1. [Difference between a service class and a Helper class](https://softwareengineering.stackexchange.com/questions/132067/difference-between-a-service-class-and-a-helper-class)
2. [Java Utility Class vs. Service](https://stackoverflow.com/questions/871434/java-utility-class-vs-service)

