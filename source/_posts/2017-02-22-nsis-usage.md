---
layout: post
title: "NSIS 用法小结"
category: "installer"
tags: ["nsis","installer"]
---




### 编译 Unicode 版本

在 nsi 文件开头加上宏配置：

```shell
Unicode true
```

#### 注意

unicode 版本的 nsi 和 nsh 文件，需要将编码设置成 **utf8 with BOM**。不然编译器会仍然以 ACP 方式读取文件。


[NSIS Users Manual](http://nsis.sourceforge.net/Docs/Contents.html)
