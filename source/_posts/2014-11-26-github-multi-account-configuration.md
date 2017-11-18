---
layout: post
title: "GitHub，GitLab的多账户配置（Windows）"
category: "git"
tags: ["git","autolayout"]
---


Windows 系统通过 SSH 方式使用 Git 时，如果有多个账户，需要手工配置多个 rsa 密钥，这里记录一下过程。下面以 GitHub 为例。

## 单个账号

### 1.生成密钥
打开 Git Bash，运行命令：

```shell
ssh-keygen -t rsa -C "your_email@example.com"
# Creates a new ssh key, using the provided email as a label
# Generating public/private rsa key pair.
# Enter file in which to save the key (/c/Users/you/.ssh/id_rsa): [Press enter]
```

下一步，输入密钥的口令：

```shell
Enter passphrase (empty for no passphrase): [Type a passphrase]
# Enter same passphrase again: [Type passphrase again]
```

执行后，将产生公钥、私钥对：

```shell
Your identification has been saved in /c/Users/you/.ssh/id_rsa.
# Your public key has been saved in /c/Users/you/.ssh/id_rsa.pub.
# The key fingerprint is:
# 01:0f:f4:3b:ca:85:d6:17:a1:7d:f0:68:9d:f0:a2:db your_email@example.com
```

### 2.将公钥、私钥加入到 ssh-agent

- 命令方式：  

```shell
# start the ssh-agent in the background
ssh-agent -s
# Agent pid 59566
ssh-add ~/.ssh/id_rsa
```

- 或者手工将2个文件复制到 user/.ssh/ 目录

### 3.将公钥文件的内容复制到 GitHub 账户中
密钥是自己保存的，公钥是需要公开给你使用的网站。登录 GitHub 账户，从账户管理里找到密钥（SSH keys）配置，新建一个key，将公钥（一串 Base64 格式）文本复制到其中，并给这个 key 标记个名字。 
 
标记个名字，方便多个账号同时使用时，方便管理。

### 4.测试
在 Git Bash 中输入:

```shell
ssh -T git@github.com
# Attempts to ssh to GitHub
```

会收到一个提示：

```shell
The authenticity of host 'github.com (207.97.227.239)' can't be established.
# RSA key fingerprint is 16:27:ac:a5:76:28:2d:36:63:1b:56:4d:eb:df:a6:48.
# Are you sure you want to continue connecting (yes/no)?
```

检查一下指纹（fingerprint）和你上面产生的密钥是否一致，然后输入`yes`.

最后，你将收到配置成功的消息 :)

```shell
Hi username! You've successfully authenticated, but GitHub does not
# provide shell access.
```

## 添加多个账户
上面操作时**手工**产生的`id_rsa`密钥文件放到`username\.ssh\`目录下，如果改成其它文件名，在连接时将出错，这就不能支持多个账号的ssh key。  

（不过，我猜可能通过命令 `ssh-add ~/.ssh/id_rsa` 添加的 key 可能会自动产生多账号配置文件。有待验证！！！）

那如果有多个同名的`id_rsa`密钥对文件如何处理呢？

做法是添加一个配置文件，来管理（映射）密钥的关系，这只是 windows 的方案，Linux 自带 SSH agent 的。

### 1.创建管理文件
在 `.ssh` 目录下创建 `config` 文件，同时将产生的 `id_rsa` 文件改成较好记的名字，比如 `username.github.com`。

### 2.添加映射
打开 `config` 文件，添加：

```shell
Host github.com
	User git
	Hostname ssh.github.com
	PreferredAuthentications publickey
	IdentityFile ~/.ssh/username.github.com
	Port 443

Host anothersite.com
	User git
	Hostname ssh.anothersite.com
	PreferredAuthentications publickey
	IdentityFile ~/.ssh/username.anothersite.com
	Port 443
```

上面是2个密钥映射的关系，`IdentityFile`是改名后的文件，其它参数根据网站需要来更改。


