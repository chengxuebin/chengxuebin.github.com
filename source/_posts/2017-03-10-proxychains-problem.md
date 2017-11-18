---
layout: post
title: "Proxychains4 问题"
category: "mac"
tags: ["shadowsocks","mac"]
---




### Timeout 问题

```shell
proxychains4 curl https://www.twitter.com/
[proxychains] config file found: /usr/local/Cellar/proxychains-ng/4.10/etc/proxychains.conf
[proxychains] preloading /usr/local/Cellar/proxychains-ng/4.10/lib/libproxychains4.dylib
curl: (7) Failed to connect to www.twitter.com port 443: Operation timed out
```

### 解决办法

#### 1. 重启系统进入维护模式（Recovery Mode）
按住 'Command' 和 'R' 启动系统

#### 2. 打开终端（Terminal）

#### 3. 禁用 csrutil

```shell
csrutil disable
```

#### 4. 重启系统
