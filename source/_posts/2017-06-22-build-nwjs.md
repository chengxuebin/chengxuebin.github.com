---
layout: post
title: "编译 NW.js"
category: "nwjs"
tags: ['nwjs']
---


本文讨论的是 NW.js 13.x 在 Windows 操作系统的编译过程。使用这一版，主要因为其支持 `xp` 和 `npapi`。

NW.js 13.x 的文档在 [v0.13.0-beta7](http://docs.nwjs.io/en/v0.13.0-beta7/For%20Developers/Building%20NW.js/)，在[这里](https://github.com/nwjs/nw.js/tree/nw13/docs)下载

NW.js 本身是一个浏览器的应用（Packaged App），它的实现思路是在每一个 `Chromium` 稳定发布版的基础上，打上补丁，以支持 `Node.js` 等功能。下面是 NW.js 打补丁的几个仓库：

|path    |repo                             |
|:-------|:---------------------------------|
|src/content/nw  |https://github.com/nwjs/nw.js|
|src/third_party/node-nw |https://github.com/nwjs/node|
|src/v8  |https://github.com/nwjs/v8|


## 准备工作

* 安装 `depot_tools`
* 安装 `DirectX SDK`

## 下载代码

#### 1. 创建配置文件

创建一个目录，然后执行下面语句，生成配置文件：

```shell
gclient config --name=src https://github.com/nwjs/chromium.src.git@origin/nw13
```

<!-- more -->

执行完毕后，会在目录里生成 `.gclient` 文件，编辑该文件，在 `custom_deps` 部分添加 **不下载** 的库，这是一些测试代码，体积还是挺大的。

```javascript
"custom_deps" : {
    "src/third_party/WebKit/LayoutTests": None,
    "src/chrome_frame/tools/test/reference_build/chrome": None,
    "src/chrome_frame/tools/test/reference_build/chrome_win": None,
    "src/chrome/tools/test/reference_build/chrome": None,
    "src/chrome/tools/test/reference_build/chrome_linux": None,
    "src/chrome/tools/test/reference_build/chrome_mac": None,
    "src/chrome/tools/test/reference_build/chrome_win": None,
}
```

#### 2. 下载代码

在命令行窗口中执行

```shell
gclient sync --with_branch_heads
```

`with_branch_heads` 表示下载全部分支记录，默认只下载最新的分支记录，由于我们想切换到特定版本 `nw13`， 所以需要下载分支记录。

#### 3. 复制依赖库

本版本需要手动复制 `DirectX SDK`，执行以下命令：

```shell
mkdir -p $HOME/nwjs/src/third_party/directxsdk/files
cp -r /c/Program\ Files\ \(x86\)/Microsoft\ DirectX\ SDK\ \(June\ 2010\)/* \
$HOME/nwjs/src/third_party/directxsdk/files/
```


## 编译

其实，在执行 `gclient sync` 的最后，会自动执行 `runhooks`，生成编译配置文件，保存在 `out` 目录。

为了配置参数方便，可以将生成项目的命令写成批处理，像下面这样：

#### 生成 release 配置 

```shell
@echo off

set DEPOT_TOOLS_WIN_TOOLCHAIN=0
set GYP_DEFINES=clang=0 nwjs_sdk=1 disable_nacl=0 buildtype=Official component=static_library
set GYP_MSVS_VERSION=2015
set GYP_GENERATORS=msvs-ninja,ninja

gclient runhooks --force

pause
```


#### 生成 debug 配置

```shell
@echo off

set DEPOT_TOOLS_WIN_TOOLCHAIN=0
set GYP_DEFINES=clang=0 nwjs_sdk=true enable_nacl=true component=shared_library
set GYP_MSVS_VERSION=2015
set GYP_GENERATORS=msvs-ninja,ninja

gclient runhooks --force

pause
```

#### GYP_DEFINES 参数 

| 参数 | 说明 |
|:----:|:------------------|
|clang| |
|nwjs_sdk| |
|enable_nacl| |
|disable_nacl| |
|target_arch| x64 |
|disable_nacl| |
|component| shared_library，static_library |


#### 编译 release 版本

```shell
@echo off

ninja -C src\out\Release nwjs

pause
```

#### 编译 debug 版本

```shell
@echo off

ninja -C src\out\Debug nwjs

pause
```

## 常见问题

### 1 `KickNextTick` 未找到

```shell
src\third_party\node\src\node.cc(4637): error2039: 'KickNextTick': is not 
a member of 'node:Environment'
```

**解决办法**

1. 查看当前的 node 分支情况：`git log`
2. 分支上有关于该 bug 的 commit，更新到该 commit：git reset --hard <SHA1>
