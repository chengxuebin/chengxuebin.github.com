---
layout: post
title: "安装 Composer"
category: "php"
tags: ["linux", "composer"]
---



Composer 是一个 PHP 依赖管理程序。可以按照下面的步骤以全局方式安装到 Linux 系统。

* 第一步：下载 composer.phar

```
cd /tmp  

curl -sS https://getcomposer.org/installer | php  
```
* 第二步：移动到全局目录

```
mv composer.phar /usr/local/bin/composer  
```

现在能够以全局方式在命令行中执行 `composer`