---
layout: post
title: "配置 Fiddler 和 WireShark 解密 HTTPS 流量"
category: "OpenSSL"
tags: ['openssl']
---


首先，推荐一篇非常好的文章：[三种解密 HTTPS 流量的方法介绍](https://imququ.com/post/how-to-decrypt-https.html)。下面记录一下这次使用和配置的步骤：

## 配置 Fiddler

#### 1. 打开 HTTPS 设置

打开菜单： `Tools` - `Telerik Fiddler Options...` - `Https`

![](/images/posts/decrypt_https/fiddler_https.png)

#### 2. 导入根证书

在 `Https` 设置标签页，打开右上角菜单 `Actions` - `Trust Root Certificate`

![](/images/posts/decrypt_https/fiddler_ca.png)

#### 3. 同意系统警告

![](/images/posts/decrypt_https/fiddler_ca2.png)

<!-- more -->

## 配置 WireShark 一：配合程序日志

#### 1. 设置环境变量

添加系统环境变量 `SSLKEYLOGFILE`

![](/images/posts/decrypt_https/wireshark_log.jpg)

#### 2. 配置 WireShark

打开菜单 `Edit` - `Preference...` - `Protocols` - `SSL`

![](/images/posts/decrypt_https/wireshark_ssl.jpg)

在 `(Pre)-Master-Secret log filename` 一栏，配置上一步产生的日志文件


## 配置 WireShark 二：配合网站私钥

私钥的方式需要配合使用网站的证书私钥，这对通常是做不到的，所以仅能用于监测自己搭建的 TLS 网站。

#### 1. 配置 WireShark

打开菜单 `Edit` - `Preference...` - `Protocols` - `SSL`

![](/images/posts/decrypt_https/wireshark_ssl.jpg)

#### 2. 配置域名和私钥

打开 `RSA keys list` 配置窗口

> 为什么是 `RSA keys` 配置呢？  
> 私钥方式只能用于解析采用 `RSA` 的 TLS 连接。

![](/images/posts/decrypt_https/wireshark_key.jpg)

* IP Address： 连接的域名或 IP 地址
* Port： 连接的端口号
* Protocol： 连接的协议，TLS 要写成 http
* Key File： 服务器证书的私钥，要求 PEM 格式
* Password： 私钥密码



 