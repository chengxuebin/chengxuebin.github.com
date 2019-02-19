---
layout: post
title: "Chomium 崩溃日志"
category: "chromium"
tags: ["chromium","crashpad"]
---

### 生成 Crash 报告

打开 Chromium，通过访问 `http://crash/` 来触发生成崩溃报告，保存在：

* Linux：`~/.config/google-chrome/Crash Reports/`
* Windows/Mac：`/path/to/profile/Crash Reports`


在 Linux 平台，通过设置环境变量，可以阻止报告发送给服务器：

```shell
$ env CHROME_HEADLESS=1 ./out/Debug/chrome-wrapper
```

<!-- more -->

### 解析 Crash 报告

可以通过 `minidump_stackwalk` 工具来解析崩溃报告：

```shell
$ minidump_stackwalk 报告名.dmp
```

能过 Linux 平台生成的报告，在执行上面命令解析前，需要先把文件的头（header）去掉，使用文本编辑器打开 dmp 文件，搜索 `MDMP` 字符，然后将此字符前的头删除后再解析。


### 参考

1. [Decoding Crash Dumps](https://www.chromium.org/developers/decoding-crash-dumps)
2. [Linux Crash Dumping](https://chromium.googlesource.com/chromium/src/+/HEAD/docs/linux_crash_dumping.md)
