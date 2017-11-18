---
layout: post
title: "CEF 的使用"
category: "c++"
tags: ["c++","cef","windows"]
---




## 下载代码

[http://opensource.spotify.com/cefbuilds/index.html](http://opensource.spotify.com/cefbuilds/index.html)

## CEF 

### cefsimple 示例

1. 编译 `libcef_dll_wrapper` 静态库
2. 创建新的项目
3. 设置 include 目录到 cef 头文件目录
4. 设置链接库  comctl32.lib, shlwapi.lib, rcprt4.lib, libcef_dll_wrapper.lib, libcef.lib, cef_sandbox.lib
5. 添加 resource.rc
6. 添加 cefsimple.exe.manifest 和 compatibility.manifest
7. 绑定编译事件 post build: 将 cef debug 或 release 目录，Resources 目录复制到输出目录

### 常见问题

#### CefSettings

当嵌入到 windows 消息循环和 DuiLib 消息循环时，要设置 `multi_threaded_message_loop`

#### CefBrowser 和 CefFrame 

CefBrowser 可以包含多个 CefFrame

#### CefApp

待补充

#### CefClient

待补充

