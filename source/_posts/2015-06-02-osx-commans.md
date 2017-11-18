---
layout: post
title: "osx 常用命令"
category: "osx"
tags: ["osx", 'command']
---



## 一、网络
### 查看谁在侦听端口

```bash
sudo lsof -n -i4TCP:$PORT | grep LISTEN
```

>[lsof](http://en.wikipedia.org/wiki/Lsof) is a command meaning "list open files", which is used in many Unix-like systems to report a list of all open files and the processes that opened them.


### 修改 Host

```bash
sudo vi /etc/hosts
```

注意 `::1` 的写法：

>如果地址中出现连续的0，则可以用“::”号来压缩(称为零压缩)，进一步简化IP v6地址表示。例如，地址FE80:0:0:0:2AA:FF:FE9A:4CA2 可以压缩成FE80::2AA:FF:FE9A:4CA2；FF02:0:0:0:0:0:0:2 可以压缩成 FF02::2。零压缩只能用于压缩冒号十六进制地址中一个连续的16位块，不能压缩部分16位信息块，例如，不能将FF02:30:0:0:0:0:0:5 表示成 FF02:3::5。用 :: 表示0位的数量用(8-n)×16公式来计算，其中n为地址中的16位块的数量。例如，在地址FF02::2中，被压缩了96=(8-2)×16个0。零压缩只能在给定地址中使用一次，否则，就无法确定每个双冒号 (::)实例所表示的 0位数量。

>特殊地址。IP v6中有两个特殊地址：
- 未指定地址(0:0:0:0:0:0:0:0 或::)：仅用于指出某个地址不存在，它等价于 IP v4 未指定地址0.0.0.0。未指定地址通常作为源地址来验证暂定地址的惟一性，但不会用作目的地址。
- 环回地址(0:0:0:0:0:0:0:1或::1)：用来标识环回接口，允许节点自发自收，它等价于IP v4环回地址 127.0.0.1。


### 统计代码行数

- 列出每个文件的行数

```bash
find . -name "*.m" -or -name "*.h" -or -name "*.xib" -or -name "*.c" |xargs wc -l  
```

- 列出代码行数总和 

```bash
find . -name "*.m" -or -name "*.h" -or -name "*.xib" -or -name "*.c" |xargs grep -v "^$"|wc -l  
```

### 查看正在运行的进程

- 通过进程名查看

```bash
ps aux|grep "Application Name" 
```

- 按 CPU 排序

```bash
top -o cpu
```

- 按内存使用排序

```bash
top -o rsize
```