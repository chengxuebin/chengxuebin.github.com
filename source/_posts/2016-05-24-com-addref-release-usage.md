---
layout: post
title: "COM 组件 AddRef/Release 用法"
category: "C++"
tags: ["vc++","com"]
---


`AddRef/Release` 是 COM 组件的垃圾回收机制。当请求一个接口时，调用`AddRef`增加引用计数；当使用完该接口时，调用`Release`减少计数，计数为0时，释放接口（释放接口对应的对象，内存如何操作？）。

### IUnknown

### QueryInterface VS QueryControl

### CoCreateInstance 

### CComQIPtr VS CComPtr

*附录*：

* [COM组件的AddRef和Release()方法使用](http://blog.csdn.net/yangshuangtao/article/details/51220110)  
* [Reference Counting Rules](https://support.microsoft.com/en-us/kb/104138)
* [COM :IUnknown接口QueryInterface函数介绍](http://www.cnblogs.com/hibernate6/archive/2009/11/12/2522119.html)
* [COM编程——引用计数（1）](http://www.jellythink.com/archives/232)
* [COM编程——引用计数（2）](http://www.jellythink.com/archives/242)
* [COM编程——QueryInterface函数（1）](http://www.jellythink.com/archives/188)
* [COM编程——QueryInterface函数（2）](http://www.jellythink.com/archives/198)