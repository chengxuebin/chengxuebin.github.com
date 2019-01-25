---
layout: post
title: "OpenSSL 常用方法收集"
category: "OpenSSL"
tags: ['openssl']
---


[下载 OpenSSL](http://onthink.com/post/openssl-resources.html)

进入 OpenSSL 命令行模式：

```shell
OpenSSL
```

;) windows 系统中会提示：

```shell
WARNING: can\'t open config file: /usr/local/ssl/openssl.cnf
OpenSSL>
```

退出OpenSSL程序

```shell
OpenSSL> exit
```


### 生成私钥

```shell
OpenSSL> genrsa -out rsa_private_key.pem   1024 
```

<!-- more -->

### 生成公钥

```shell
OpenSSL> rsa -in rsa_private_key.pem -pubout -out rsa_public_key.pem
```

### 将公私钥写入同一文件

```shell
cat cert.key cert.crt > cert.pem
```

### 转换成 PKCS8 格式

```shell
OpenSSL> pkcs8 -topk8 -inform PEM -in rsa_private_key.pem -outform PEM -nocrypt -out rsa_private_key_pkcs8.pem 
```

其它一些格式转换，参考[这里](http://onthink.com/post/about-cert-type.html)

### 参考资源

1. [Nginx https 双向认证](https://www.cnblogs.com/yelao/p/9486882.html)

