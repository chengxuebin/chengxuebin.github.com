---
layout: post
title: "使用 VS2015 编译 boost"
category: "c++"
tags: ["c++","boost"]
---


[boost 官方文档](http://www.boost.org/doc/libs/1_63_0/more/getting_started/windows.html)

### 最简单的编译方法

```shell
bootstrap.bat
.\b2.exe
```

当编译成功，会得到提示


```shell

The Boost C++ Libraries were successfully built!

The following directory should be added to compiler include paths:

    C:\Users\jon\Desktop\boost_1_63_0

The following directory should be added to linker library paths:

    C:\Users\jon\Desktop\boost_1_63_0\stage\lib

```


### 编译并指定目录

```shell
./b2 install --prefix=PREFIX
```

[参考](http://www.boost.org/doc/libs/1_55_0/doc/html/bbv2/installation.html)


### 编译参数示例

```shell
.\b2 install --prefix=C:\Users\jon\Desktop\builds\boost --toolset=msvc --without-python link=static runtime-link=shared debug release
```


### 使用 boost 头文件库（Header Only）

将下载的 boost 目录添加到项目的 include 路径即可

### 使用 boost 编译库（Header+CPP）

除了将 boost 目录添加到 include，还要将上面编译出来的 `stage/lib` 目录添加到 library 路径

