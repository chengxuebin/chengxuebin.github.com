---
layout: post
title: "Chomium 调试方法"
category: "chromium"
tags: ["chromium","dbg"]
---

## Linux 平台

### 基础调试

```shell
$ gdb -tui -ex=r --args out/Debug/chrome --disable-seccomp-sandbox http://google.com
```

### 参考

1. [Tips for debugging on Linux](https://chromium.googlesource.com/chromium/src/+/HEAD/docs/linux_debugging.md#Basic-browser-process-debugging)
