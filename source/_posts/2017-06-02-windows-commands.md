---
layout: post
title: "windows 常用命令"
category: "windows"
tags: ["windows", 'command']
---



## 一、网络

### 查看谁在侦听端口

```shell
netstat -np <protocol> | find "port #"

netstat -np TCP | find "80"
```


### 打开 Internet 网络设置

```shell
inetcpl.cpl
```

其它的控制命令

```shell
appwiz.cpl
bthprops.cpl
desk.cpl
Firewall.cpl
hdwwiz.cpl
igfxCPL.cpl
inetcpl.cpl
intl.cpl
irprops.cpl
joy.cpl
main.cpl
mmsys.cpl
ncpa.cpl
powercfg.cpl
sysdm.cpl
TabletPC.cpl
telephon.cpl
timedate.cpl
wscui.cpl
```

<!-- more -->

## 二、文件系统

### 以 Ascii 树列出文件和目录

打开 `PowerShell`，执行：

```shell
tree <文件目录> /F /A
```

