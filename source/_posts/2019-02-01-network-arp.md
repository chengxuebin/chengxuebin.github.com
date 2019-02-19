---
layout: post
title: "网络工具：ARP"
category: "network"
tags: ["network",'arp']
---

### 1 查看 arp 映射列表

```shell
$ arp -a
```

### 2 清除 arp 映射

```shell
# 清除一个映射
$ arp -d 192.168.199.1

# 清除全部映射
$ arp -ad
```

<!-- more -->

