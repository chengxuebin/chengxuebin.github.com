---
layout: post
title: "解决 Windows 远程桌面连接失败"
category: "修理"
tags: ["windows"]
---

### 1 修改防火墙

1. 打开 `控制面板` > `安全` > `防火墙`
2. 打开 `Allow an app or feature through Windows Firewall`
3. 找到 `Remote Desktop`，选中 `Private`

### 2 检查远程访问是否被允许

1. 通过搜索，打开 `Allow Remote connections to your computer`
2. 选中 `Allow Remote Assistance connections to this computer`
3. 选中 `Allow remote connections to this computer`

<!-- more -->

### 3 Windows 10：检查远程访问是否被允许

1. 依次打开：`Settings` > `System` > `Remote Desktop`
2. 启用 `Enable Remote Desktop`

### 4 Windows 10：禁止 IPv6

依次打开：`Settings` > `Network and Internet` > `Ethernet` > `Change Adapter options`，取消选中 `Internet Protocol Version 6 (TCP/IPv6)`，然后**重启机器**。


