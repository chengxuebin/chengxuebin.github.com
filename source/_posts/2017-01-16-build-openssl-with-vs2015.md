---
layout: post
title: "使用 vs2015 编译 OpenSSL"
category: "openssl"
tags: ["openssl","vs2015"]
---


### OpenSSL 版本

openssl-1.0.2j

### 必需的工具

* Visual Studio 2015
* ActivePerl
* Nasm (The Netwide Assembler)

nasm 需要根据需要下载32位或64位版本：

* [32位](http://www.nasm.us/pub/nasm/releasebuilds/2.12.02/win32/)
* [64位](http://www.nasm.us/pub/nasm/releasebuilds/2.12.02/win64/)


### 编译32位版本

#### 1. 初始化命令行环境

```shell

cd C:\build\src\openssl-1.0.2j

set PATH=%PATH%;C:\nasm

"C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\vcvarsall.bat"

```


#### 2. 初始化编译配置

```shell
perl Configure VC-WIN32 no-asm

ms\do_nasm
```


#### 3. 编译

```shell
// 静态库
nmake /f ms\nt.mak
nmake /f ms\nt.mak test
nmake /f ms\nt.mak install

// 动态库
nmake -f ms\ntdll.mak
nmake -f ms\ntdll.mak install
```



### 编译64位版本

#### 1. 初始化命令行环境

```shell

cd C:\build\src\openssl-1.0.2j

set PATH=%PATH%;C:\nasm

"C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\vcvarsall.bat" amd64

```


#### 2. 初始化编译配置

```shell
perl Configure VC-WIN64A no-asm

ms\do_win64a
```


#### 3. 编译

```shell
// 静态库
nmake /f ms\nt.mak
nmake /f ms\nt.mak test
nmake /f ms\nt.mak install

// 动态库
nmake -f ms\ntdll.mak
nmake -f ms\ntdll.mak install
```



### 遇到的错误

```shell
Assembling: tmp32\sha1-586.asm
tmp32\sha1-586.asm(1427) : error A2070: invalid instruction operands
tmp32\sha1-586.asm(1571) : error A2070: invalid instruction operands
NMAKE : fatal error U1077: 'ml' : return code '0x1'
Stop.
```

解决： `perl Configure VC-WIN32 no-asm` 加上 no-asm




