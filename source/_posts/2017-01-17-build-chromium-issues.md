---
layout: post
title: "编译 Chromium 常见问题"
category: "chromium"
tags: ["chromium"]
---

## 配置

### 命令行翻墙设置

**Windows 命令行设置**

```shell
set http_proxy=127.0.0.1:1080
set https_proxy=127.0.0.1:1080
set socks5_proxy=127.0.0.1:1080
```

**Git 设置**

```shell
git config --global http.proxy 127.0.0.1:1080
git config --global https.proxy 127.0.0.1:1080
```

## 获取代码

### gclient sync 参数

#### --nohooks

#### --no-history

#### --with_branch_heads

#### --with_tags

#### --output-json

## 编译

### DEPOT_TOOLS_WIN_TOOLCHAIN

### GYP_GENERATORS=msvs-ninja,ninja

### GYP_DEFINES

### buildtype=Official

如果不添加 `buildtype=Official` 可能会遇到下面问题：

1. XP 系统不能正常运行


在添加了该编译参数情况下，也会遇到某些奇怪问题：

1. 在某些机器上，当打开某些页面，比如[新浪视频](http://video.sina.com.cn/p/news/o/doc/2018-06-12/151568884855.html)，退出浏览器后，会收到网络层不能析构的 bug

### gclient runhooks

gclient runhooks 会做二件事：一是检查编译环境，二是生成编译脚本。我们也可以跳过检查编译环境，直接生成编译脚本：

**生成全部脚本**

```shell
python src\build\gyp_chromium
```

**生成某个工程**

```shell
# chrome 工程
python src\build\gyp_chromium src\chrome\chrome.gyp
# 安装包工程
python src\build\gyp_chromium src\chrome\installer\mini_installer.gyp
```

Goutput_dir

## 错误

### 查找错误

```shell
ninja -C src/out/Debug chrome | findstr error
```

### VS 忽略错误

VS2015 编译时会遇到一些错误，通过以下设置解决

```
4334, # 
4595, # 
4275, # 
4819, # The file contains a character that cannot berepresented in the current code page (936).
```

### gclient sync 错误

```shell

fatal: Needed a single revision
Does not point to a valid commit: 4ce7fef091e1d63a3bfc2ed225619893b0eb1782
32> Unrecognized error, please merge or rebase manually.  
32> cd src\third_party\libjingle\source\talk && git rebase --onto 4ce7fef091e1d63a3bfc2ed225619893b0eb1782 refs/remotes/origin/master
```

**解决办法：**

```shell
gclient sync --with_branch_heads
```

### Windows 系统语言错误

```shell
> The file contains a character that cannot berepresented in the current code page.
```

**解决办法：**

依次打开：Region and Language > Administrative > Langeuage for non-Unicode programs 

点击 Change system locale...，切换为 English (United State)