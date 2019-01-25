---
layout: post
title: "Linux 浏览器如何配置 PKCS#11 设备"
category: "browser"
tags: ["android","修理"]
---

Linux 常用的浏览器包括 Firefox 和 Chrome，这里演示如何为他们添加 PKCS#11（[什么是 PKCS#11？](https://en.wikipedia.org/wiki/PKCS_11)） 设备。

在 Linux 平台上，不管是 Firefox 还是 Chrome 都是使用的 NSS（[什么是 NSS？](https://en.wikipedia.org/wiki/Network_Security_Services)） 来管理证书和安全设备。所以我们只需要知道如何管理 NSS，就可以实现配置这两个浏览器的安全设备配置。

<!-- more -->

### NSS 工具

* certutil

文档参考[这里](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/certutil)

* modutil

文档参考[这里](https://developer.mozilla.org/en-US/docs/Mozilla/Projects/NSS/Reference/NSS_tools_:_modutil)

### Firefox

Firefox 自带安全设备的配置界面，所以直接通过界面添加 PKCS#11 设备。

**第一步：打开配置**

![](/images/posts/nss/ff1.png)

**第二步：进入安全设备管理**

![](/images/posts/nss/ff2.jpg)

**第三步：载入安全设备**

![](/images/posts/nss/ff3.jpg)

**第四步：载入成功**

![](/images/posts/nss/ff4.jpg)

**第五步：输入 PKCS#11 设备 PIN 码**

![](/images/posts/nss/ff5.png)


### Chrome

**添加 PKCS#11 设备**

添加或修改 nss 数据库前，先关闭 chrome 浏览器，然后打开终端，运行下面命令：

```shell
$ modutil -dbdir sql:/home/user-x/.pki/nssdb/ -add "opensc" -libfile  /home/user-x/Desktop/P11/libTFTknP11.so
```
添加成功

```shell
WARNING: Performing this operation while the browser is running could cause
corruption of your security databases. If the browser is currently running,
you should exit browser before continuing this operation. Type 
'q <enter>' to abort, or <enter> to continue: 

DeviceInfo: �2
DeviceInfo: �2
Module "opensc" added to database.
```

**查看设备列表**

```shell
$ sudo modutil -dbdir sql:/home/user-x/.pki/nssdb/ -list
```

显示结果，`2. opensc` 是新添加的设备

```shell
Listing of PKCS #11 Modules
-----------------------------------------------------------
  1. NSS Internal PKCS #11 Module
   slots: 2 slots attached
  status: loaded

   slot: NSS Internal Cryptographic Services
  token: NSS Generic Crypto Services

   slot: NSS User Private Key and Certificate Services
  token: NSS Certificate DB

  2. opensc
  library name: /home/user-x/Desktop/P11/libTFTknP11.so
   slots: 1 slot attached
  status: loaded

   slot: SDKEY Slot 0201
  token: MY_TOKEN
-----------------------------------------------------------
```

**打开 Chrome 查看**

通过 Chrome 打开 `chrome://settings/certificates`，浏览器会读取配置的 PKCS#11 设备，提醒用户输入设备 PIN 码

![](/images/posts/nss/chrome1.jpg)

**访问双向验证网站**

访问需要验证客户端证书的网站时，浏览器也会读取配置的 PKCS#11 设备，提醒用户输入设备 PIN 码

![](/images/posts/nss/chrome2.jpg)

### 常见问题

#### 1 SEC_ERROR_BAD_DATABASE

```shell
Description of problem:
apps get "SEC_ERROR_BAD_DATABASE: security library: bad database." error when they set db type by "sql:${DIR}" (but work fine when type is set by NSS_DEFAULT_DB_TYPE env var)
```

**解决办法**

数据库的路径没有写正确，需要使用*绝对路径*。