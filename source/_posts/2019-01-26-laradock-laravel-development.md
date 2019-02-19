---
layout: post
title: "使用 Laradock 创建 Laravel 开发环境"
category: "php"
tags: ["laravel","docker",'nginx','laradock','mysql','php']
---

## Docker Desktop

从官网下载用于 Mac 机器的 Docker Desktop，如果测试从官方源下载镜像非常慢的话，可以添加国内的阿里加速镜像：

![](/images/posts/laradock/docker-desktop-1.jpg)

## 配置 Laradock

### 1 下载 Laradock

```shell
$ git clone https://github.com/laradock/laradock.git
```

目录结构如下

```
+ laradock
+ project-1
+ project-2
```

### 2 配置虚拟主机

前往 `nginx/sites`，复制 `laravel.conf.example` 为 `project-1.test.conf`

**注意：**只有 `.conf` 结尾的才会被 nginx 读取。

<!-- more -->

```json
server {

    listen 80;
    listen [::]:80;

    # 域名
    server_name project-1.test;
    # 项目目录，均以 /var/www/ 开头
    root /var/www/project-1;
    index index.php index.html index.htm;

    location / {
         try_files $uri $uri/ /index.php$is_args$args;
    }

    location ~ \.php$ {
        try_files $uri /index.php =404;
        fastcgi_pass php-upstream;
        fastcgi_index index.php;
        fastcgi_buffers 16 16k;
        fastcgi_buffer_size 32k;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.ht {
        deny all;
    }

    location /.well-known/acme-challenge/ {
        root /var/www/letsencrypt/;
        log_not_found off;
    }

    error_log /var/log/nginx/laravel_error.log;
    access_log /var/log/nginx/laravel_access.log;
}
```

### 3 修改本机 hosts

```
127.0.0.1  project-1.test
127.0.0.1  project-2.test
...
```

### 4 配置 laradock 的 `.env` 

前往 laradock 目录，执行

```shell
$ cp env-example .env
```

打开 `.env` 文件，根据个人需要定制 PHP、Nginx、MySQL 版本及参数。

### 5 启动

```shell
$ docker-compose up -d nginx mysql
```

### 6 测试

浏览器打开 `http://project-1.test`，显示出你的 PHP 网站

## 开发

开发 laravel 通常我们需要一些工具，比如 composer，PHPUnit，nodejs 等，Laradock 环境已经为我们安装好了这些工具，但这些环境是在 docker 内部，我们需要通过命令将工作目录映射到 docker 环境，这是要执行的命令

```shell
$ docker-compose exec workspace bash
```

## 常见问题

### 1 停止 docker-compose

```shell
$ docker-compose stop
```

### 2 切换组件版本

比如将 MySQL 版本从 latest 切换成 5.7，然后执行以下命令

```shell
$ docker-compose build --no-cache php-fpm
```

### 3 重启组件

重启 Nginx：

```shell
$ docker-compose restart nginx
```

### 4 添加组件

比如，添加 phpmyadmin：

```shell
$ docker-compose up -d nginx mysql phpmyadmin
```

### 5 MySQL 服务器的 Host

MySQL 服务器绑定的 Host 可以不填写具体 ip 地址，而是简单地用 mysql 表示.

### 6 workspace 和 php-fpm 的区别

环境配置文件 `.env` 里包含相似的两块配置：workspace 和 php-fpm，它们对应两个不同的容器，一个是 FPM，一个是 CLI。不管是安装插件还是修改配置，都要分开修改。

### 7 删除 `<none>` 镜像

```shell
$ docker rmi $(docker images --filter "dangling=true" -q --no-trunc)   
```

### 8 清除 Container，Image

```shell
docker system prune

WARNING! This will remove:
        - all stopped containers
        - all networks not used by at least one container
        - all dangling images
        - all build cache
```
