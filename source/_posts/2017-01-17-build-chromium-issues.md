---
layout: post
title: "编译 Chromium 常见问题"
category: "chromium"
tags: ["chromium"]
---



### gclient sync 错误

```shell

fatal: Needed a single revision
Does not point to a valid commit: 4ce7fef091e1d63a3bfc2ed225619893b0eb1782
32> Unrecognized error, please merge or rebase manually.  
32> cd src\third_party\libjingle\source\talk && git rebase --onto 4ce7fef091e1d63a3bfc2ed225619893b0eb1782 refs/remotes/origin/master
```

解决办法：

```shell
gclient sync --with_branch_heads
```
