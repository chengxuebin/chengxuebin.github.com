---
layout: post
title: "Laradock 配置 xdebug 与 phpstorm"
category: "php"
tags: ["php","laradock","phpstorm"]
---

## 1 配置 Laradock

### 1.1 修改 laradock/.env 

打开 `.env` 文件，修改下列配置项：

```ini
WORKSPACE_INSTALL_XDEBUG=true
PHP_FPM_INSTALL_XDEBUG=true
```

### 1.2 配置 laradock/php-fpm/xdebug.ini

php-fpm 用于调试 php 的服务器模式，基于浏览器请求。

```ini
; NOTE: The actual debug.so extention is NOT SET HERE but rather (/usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini)

; xdebug.remote_host=docker.for.mac.host.internal
; 这里要注意，很多文章说无须修改此项，但我的环境不行，必须配置
; 关于 host.docker.internal，请参考：https://docs.docker.com/docker-for-mac/networking/#there-is-no-docker0-bridge-on-macos
xdebug.remote_host=host.docker.internal
xdebug.remote_connect_back=0
; 配置端口号
xdebug.remote_port=9001
; 配置 key
xdebug.idekey=PHPSTORM

; 开启
xdebug.remote_autostart=1
; 开启
xdebug.remote_enable=1
xdebug.cli_color=0
xdebug.profiler_enable=0
xdebug.profiler_output_dir="~/xdebug/phpstorm/tmp/profiling"

xdebug.remote_handler=dbgp
xdebug.remote_mode=req

xdebug.var_display_max_children=-1
xdebug.var_display_max_data=-1
xdebug.var_display_max_depth=-1

```

<!-- more -->


### 1.3 配置 laradock/workspace/xdebug.ini

workspace 用于调试 php 的命令行模式

```ini
; NOTE: The actual debug.so extention is NOT SET HERE but rather (/usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini)

xdebug.remote_host=host.docker.internal
xdebug.remote_connect_back=0
xdebug.remote_port=9001
xdebug.idekey=PHPSTORM

xdebug.remote_autostart=1
xdebug.remote_enable=1
xdebug.cli_color=0
xdebug.profiler_enable=0
xdebug.profiler_output_dir="~/xdebug/phpstorm/tmp/profiling"

xdebug.remote_handler=dbgp
xdebug.remote_mode=req

xdebug.var_display_max_children=-1
xdebug.var_display_max_data=-1
xdebug.var_display_max_depth=-1

```

### 1.4 重新编译 php-fpm 和 workspace 容器

*这里要注意，修改了 xdebug.ini 一定要重新编译容器*

```shell
$ docker-compose build --no-cache php-fpm workspace
```

### 1.5 重新启动容器

```shell
$ docker-compose up -d nginx
```

## 2 配置 PhpStorm

### 2.1 配置 PHP

打开 `Preferences > Languages & Frameworks > PHP`

![](/images/posts/xdebug/phpstorm1.png)

### 2.2 配置 DBGp Proxy

打开 `Preferences > Languages & Frameworks > PHP > DBGp Proxy`

![](/images/posts/xdebug/phpstorm2.png)

### 2.3 添加服务器 

打开 `Preferences > Languages & Frameworks > PHP > Server`

![](/images/posts/xdebug/phpstorm3.png)

