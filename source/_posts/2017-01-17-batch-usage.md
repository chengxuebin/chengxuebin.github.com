---
layout: post
title: "Windows Batch 用法"
category: "windows"
tags: ["windows","bat"]
---


#### 当前脚本目录

```shell
%~dp0
```

#### 当前目录

```shell
for %%* in (.) do set CurrentDirName=%%~nx*
```

#### 当前目录（不带斜线 ）

```shell
SET mypath=%~dp0
echo %mypath:~0,-1%
```

