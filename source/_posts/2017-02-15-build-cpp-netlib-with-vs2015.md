---
layout: post
title: "使用 VS2015 编译 cpp-netlib"
category: "c++"
tags: ["c++","boost"]
---


[cpp-netlib 官方文档](http://cpp-netlib.org/0.11.0/getting_started.html#building-on-windows)

cpp-netlib 旨在用最新的 C++ 标准，提供易用的网络接口库。

* [官方网站](http://cpp-netlib.org/)：官网提供最新的代码和库下载
* [GitHub](https://github.com/cpp-netlib/cpp-netlib)：提供较早版本的下载


## 编译步骤

#### 1. 编译 boost 

```shell
bootstrap.bat
.\b2 install --prefix=C:\Users\jon\Desktop\builds\boost --toolset=msvc --without-python link=static runtime-link=shared debug release
```

* `runtime-link=shared` 是使用动态库方式编译，对应 VS 设置值 `runtime = MD`
* `runtime-link=static` 是使用静态库方式编译，对应 VS 设置值 `runtime = MT`


#### 2. 生成 cpp-netlib 配置文件

```shell
mkdir cpp-netlib-build

cd cpp-netlib-build

cmake -G "Visual Studio 14" -DCMAKE_BUILD_TYPE=Debug -DBOOST_ROOT=C:/Users/jon/Desktop/builds/boost ..\cpp-netlib-0.12.0-final
```

其它的设置选项还有：

* -DCMAKE_BUILD_TYPE=Debug 
* -DCMAKE_C_COMPILER=clang
* -DCMAKE_CXX_COMPILER=clang++ 

#### 3. 打开 `cpp-netlib-build/CPP-NETLIB.sln` 编译

可以全部编译，也可以根据需要单独编译某个模块。比如，单独编译 `cppnetlib-uri` 项目。编译好的文件输出在 `cpp-netlib-build/libs/network/src/Release`


## 遇到的问题

### 1. Invalid escape sequence \U

检查 `-DBOOST_ROOT=` 后面的路径分隔符是不是反斜扛。
