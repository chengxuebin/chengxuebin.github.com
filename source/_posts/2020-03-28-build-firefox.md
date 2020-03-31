---
layout: post
title: "编译 Firfox"
category: "浏览器"
tags: ["firefox","浏览器"]
---

#### 在线代码

从[这里](https://hg.mozilla.org/mozilla-central/file/tip/netwerk/socket)在线浏览代码。

#### tarball

tarball 是代码仓库的快照，打包了某个版本的全部代码，因为做了压缩，比直接`clone`下载的数据量更小，速度更稳定，也多了断点续传。国内推荐用这种方式下载，参考[这里](https://hg.cdn.mozilla.net/)。下载后解压成仓库的流程参考这里：[解压 bundle](https://developer.mozilla.org/en-US/docs/Mozilla/Developer_guide/Source_Code/Mercurial/Bundles)。

#### Mozconfig

一些编译时的配置需要放到 Mozconfig 文件里，参考[这里](https://developer.mozilla.org/en-US/docs/Mozilla/Developer_guide/Build_Instructions/Configuring_Build_Options)。

#### 编译

编译流程参考：[这里](https://developer.mozilla.org/en-US/docs/Mozilla/Developer_guide/Build_Instructions/Simple_Firefox_build/Linux_and_MacOS_build_preparation)。

其中遇到一些技术点：

**artifact**：这种方式不用编译C++相关的代码，编译速度更快。适合于面向 Java 语言的开发者。


#### Developer guide

和 Firefox 开发相关的说明全在[这里](https://developer.mozilla.org/en-US/docs/Mozilla/Developer_guide)。

#### Working with Mozilla source code

和 Mozilla 代码相关的说明全在[这里](https://developer.mozilla.org/en-US/docs/Mozilla/Developer_guide/Source_Code)。包括如何检出代码，提交代码，编译等。


#### Mozilla Source Code Directory Structure

源码目录结构，当你需要进一步熟悉各模块时，首先要区分都有哪些模块，参考：[这里](https://developer.mozilla.org/en-US/docs/Mozilla/Developer_guide/Source_Code/Directory_structure)。一般我比较关心的几个模块：

**browser**：这里是浏览器的界面和主题相关代码

**netwerk**：网络套件库，也叫 Necko

**security**：包含 `NSS` 和 `PSM` 两套安全套件，`NSS` 包含了用于 SSL 的密码算法

**view**：一套跨平台的 UI 组件库，承担绘图、事件处理、滚动、透明化等

**widget**：它是 `view` 的宿主，是 `view` 与操作系统原生的 UI 系统的枢纽


#### 网络层 Necko

[Necko](https://developer.mozilla.org/en-US/docs/Mozilla/Projects/Necko) 是一个跨平台的网络库，提供了网络多层的接口。