---
layout: post
title: "DuiLib 的使用"
category: "c++"
tags: ["c++","duilib","windows"]
---




## 下载代码

本文下载的是 [Redrain 维护的版本](https://github.com/redrains/DuiLib_Redrain)，原始的 DuiLib 版本从[这里](https://github.com/duilib/duilib)下载。


## 以库方式使用

### 打开并升级工程文件

打开 `DuiLib_vs2010.sln`，我安装的 IDE 是 `VS2015`，会提示升级，按提示完成。

### 编译全部动态库

打开菜单 Build > Batch Build...

选择编译所有配置：

![](/images/posts/duilib/build-batch.jpg)

保存编译的结果：

* DuiLib.dll
* DuiLib.lib
* DuiLib_d.dll
* DuiLib_d.lib

以及 DuiLib 库的定义目录： `DuiLib`

当创建新工程时，把 lib 文件添加到 link 依赖里，把 DuiLib 目录添加到 inluce 路径中，最后把 dll 库放到导出目录中，便配置好了一个 DuiLib 工程。


## 以源码方式使用

### 1. 创建一个空工程

### 2. 将源码中的 DuiLib 目录添加到工程根目录中

### 3. 导入 DuiLib 项目

在工程上右键，选择：Add > Existing Project...，找到 DuiLib 项目文件导入

### 4. 设置依赖 DuiLib

在主项目上右键，选择：Build Dependencies > Project Dependencies...，设置依赖 DuiLib 项目。

这样，在编译主项目时会先编译依赖的 DuiLib 项目。


