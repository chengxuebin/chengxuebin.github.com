---
layout: post
title: "Linux 常用命令"
category: "linux"
tags: ["linux","server"]
---



## 1 打开服务器的被禁用（blocked）端口（port）

- 启用被 iptables 禁用的 80 端口

```bash
sudo /sbin/iptables -I INPUT -p tcp -m tcp --dport 80 -j ACCEPT
```

- 将上面的配置作为规则保存到 iptables，以便服务器重启后，能再次生效

```bash
sudo /sbin/service iptables save
```

## 2 通过 systemctl 启动服务

- 启动、停止、重启

```bash
# systemctl start nginx.service
# systemctl stop nginx.service
# systemctl restart nginx.service

```

除了 nginx.service, 常用的服务还有：php-fpm.service, mysqld.service

- 设置开机自启

```bash
# systemctl enable nginx.service
```

## 3 查看进程信息

```bash
# ps -aux | grep 关键字
```


## 4 查看进程的安装位置

```bash
ps -ef | grep tomcat
```


## 5 sudo 保留系统环境

```shell
sudo -E env
```


## 6 查找最大目录或文件 

### 1）查找最大目录

```shell
du -a | sort -n -r | head -n 5
```

### 2）查找最大文件

```shell
find -type f -exec du -Sh {} + | sort -rh | head -n 5
```

[参考](https://www.tecmint.com/find-top-large-directories-and-files-sizes-in-linux/)

## 7 挂载

```shell
$ sudo mount -t vboxsf 源媒体 目标路径
```

## 8 查找文件包含字符

```shell
# This will only search through those files which have .c or .h extensions:

$ grep --include=\*.{c,h} -rnw '/path/to/somewhere/' -e "pattern"

# This will exclude searching all the files ending with .o extension:

$ grep --exclude=*.o -rnw '/path/to/somewhere/' -e "pattern"

#For directories it's possible to exclude a particular directory(ies) through --exclude-dir parameter. For example, this will exclude the dirs dir1/, dir2/ and all of them matching *.dst/:

$ grep --exclude-dir={dir1,dir2,*.dst} -rnw '/path/to/somewhere/' -e "pattern"
```