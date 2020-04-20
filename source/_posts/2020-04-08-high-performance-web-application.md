---
layout: post
title: "高并发Web项目"
category: "高并发"
tags: ["web应用","高并发"]
---

#### Load average

负载对应的是一个数字：`load average: 1.02  1.04  1.30`，它们分别表示：1分钟、5分钟、15分钟内系统的平均负载。这里面最关键的是15分钟负载，相对于1分钟反应的是短时状况，它反应的是一定时间的压力。

可以将一个cpu内核理解为一条车道，负载为0表示无车，0.5表示车道内有一半的车，1表示车道内已经开满了车，超过1则表示已经有车在等待进入车道。所以对于一个内核，负载不要长期超过1。

如果有多个物理cpu，每个cpu又有多个内核，则不应该超过的负载为：`cpu数 x 内核数`

**OSX**：

```shell
# 查看负载
> sysctl -n vm.loadavg

# 查看cpu
> sysctl machdep.cpu 
```

查看CPU重要的参数是：

```shell
# CPU 内核数
machdep.cpu.core_count: 4   
machdep.cpu.thread_count: 8
```

**Linux**：

```shell
# 查看负载
> top

# 查看cpu
> cat /proc/cpuinfo
```

### Tomcat 优化配置

__maxThreads__：允许的最大线程数，默认值为200

__acceptCount__：当请求数超过最大线程数时，接受排队的请求个数，默认值为100

* 如果设的较小，超出的请求会就直接被拒绝
* 如果设的较大，可能就会出现大量的请求超时

> As a general rule of thumb, a lightweight, high performance application should look at using a maximum of 150 (accept) threads per CPU core (so a total of 600 on a 4 core box). A more conservative setting, for more heavyweight applications would be 300 accept threads. I’d expect most requirements to be somewhere around the middle (but this will need some analysis), but this is highly situational 
 

### JMeter

__TPS__：Transaction Per Second 事务处理能力，每秒处理事物数（打开页面、登录、选择商品、加入购物车、下单、付款）

主要功能模块：

* 取样器：进行脚本逻辑控制。
* 线程组：压测场景控制。
* 监视器：监控压测运行，获取性能指标。

### 参考

* [理解Linux系统负荷](https://www.ruanyifeng.com/blog/2011/07/linux_load_average_explained.html)
* [性能测试 —— Tomcat、Jetty、Undertow 基准测试](http://www.iocoder.cn/Performance-Testing/Tomcat-Jetty-Undertow-benchmark/)
* [干货收藏！史上最强 Tomcat 8 性能优化来啦！| 原力计划](https://zhuanlan.zhihu.com/p/96692243)
* 

