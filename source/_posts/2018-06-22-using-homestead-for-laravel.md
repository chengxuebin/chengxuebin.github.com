---
layout: post
title: "使用 Homestead 开发 Laravel"
category: "php"
tags: ['php', 'laravel']
---

Homestead 是基于 vagrant 的用于开发 php 程序的配置文件，包含了常用的开发软件和服务。 vagrant 是一套虚拟机管理软件，它通过虚拟机的接口管理虚拟机镜像。

**Vagrant VS 直接使用虚拟机**

* Vagrant 可以下载制作好的镜像文件，包含了特定的系统和软件组合
* Vagrant 可以通过接口直接管理镜像的启动、停止、共享目录等

**Vagrant VS Docker**

* Docker 本身是一种不同于虚拟机的虚拟化技术，它比虚拟机更轻量，常用于虚拟软件，而虚拟机则虚拟完整系统
* Vagrant 是管理虚拟机的工具，可用来管理 VMware，VirtualBox 等


下面以 virtualbox + vagrant + homestrad 为例，记录一下流程：

#### 1 安装 virtualbox

#### 2 安装 vagrant

下载 [vagrant](http://www.vagrantup.com/downloads.html)

#### 3 安装 Homestead Vagrant Box

打开命令行，直接执行

```shell
vagrant box add laravel/homestead
```

#### 4 安装 Homestead

这里直接将 homestead 安装到项目中，首先 cd 到项目目录，执行

```shell
composer require laravel/homestead --dev
```

生成 homestead 配置文件，

**Mac / Linux:**

```shell
php vendor/bin/homestead make
```

**Windows:**

```shell
vendor\bin\homestead make
```

执行成功后，会在项目根目录生成 `Homestead.yaml`


#### 5 配置 Homestead

打开 `Homestead.yaml`

##### 5.1 修改 provider

```
provider: virtualbox
```

##### 5.2 修改共享目录

```
folders:
    - map: 'c:\dev\code'   <- 对应主机的目录
      to: /home/vagrant/Code  <- 对应虚拟机的目录
```


##### 5.3 配置 Ningx 

```
sites:
    - map: homestead.app   <- 域名
      to: /home/vagrant/Code/Laravel/public
```

当开发定义了域名后，需要修改系统的 hosts 文件，Mac and Linux 文件在 `/etc/hosts`，  Windows 文件位置为 `C:\Windows\System32\drivers\etc\hosts`，打开文件后，加入下面这行：

```
192.168.10.10  homestead.app
```

#### 6 启动 Homestead

一旦配置好，通过命令 `vagrant up` 启动。其它命令：

* `vagrant destroy --force` 删除虚拟机
* `vagrant halt` 停止
* `vagrant reload` 重新加载虚拟机


#### 7 Laravel 项目初始化

**7.1 安装 php 依赖**

```shell
composer install
```

**7.2 安装 npm 依赖**

```shell
npm install
```

**7.3 编译前端资源**

```shell
npm run dev  
或 npm run build  <- 压缩、混淆处理  
或 npm run watch  <- 动态编译  
```


#### 8 测试页面

用浏览器打开 `homestead.app`，正常情况能够看到 laravel 项目首页


### 参考

* [Laravel Homestead (5.1)](https://laravel.com/docs/5.1/homestead)