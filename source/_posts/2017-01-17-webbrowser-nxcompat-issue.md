---
layout: post
title: "WebBrowser 运行 ActiveX 异常"
category: "activex"
tags: ["activex","webbrowser"]
---


#### 问题描述

前段时间使用 VS2015 编译 WebBrowser 的程序，然后运行包含 ActiveX 的页面时，出现了一些奇怪的问题（呃...问题记不清了）。但是，换成 VS2008 或其它低版本编译器时，又不会出现该问题了。

#### 解决

经过一翻查找，发现是编译参数 `/NXCOMPAT` 引起的，早期的 VS，该参数默认为 `NO`，新版本默认为 `YES`。可修改默认值为 `NO` 解决此问题。

#### 参考

> [WebBrowser无法显示招商银行密码输入控件的问题](http://blog.csdn.net/charlessimonyi/article/details/30479131)

