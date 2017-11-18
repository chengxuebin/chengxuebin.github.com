---
layout: post
title: "Amazon EC2 启用 root 登录"
category: "php"
tags: ["linux","server"]
---



在亚马逊安装完 EC2 云主机实例，当使用 root 账号登录时，会收到下面的信息：

>“Please login as the user “centos” rather than the user “root”.” Then the connection will close.

如果你想启用root账号 SSH 登录，可以按下面的步骤：


* 第一步：先登录云主机

```bash
# ssh -i privateKey.pem  root@ip-address 
```

* 第二步：切换到 root 模式

```bash
# sudo -s
```

* 第三步：修改密钥验证文件
打开 `authorized_keys`，删除从开头到 `sh-rsa` 之前的内容。

```bash
# vi /root/.ssh/authorized_keys
```

* 第四步：修改ssh配置文件

打开 `sshd_config`，注释掉 `PermitRootLogin yes`

```bash
# vi /etc/ssh/sshd_config
```

* 第五步：重启 SSH 守护进程（demand）


```bash
# systemctl restart sshd
```

现在，你可以重新以 root 用户登录实例了。