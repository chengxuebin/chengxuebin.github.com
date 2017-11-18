---
layout: post
title: "osx安装php扩展"
category: "chromium"
tags: ["osx", 'extension']
---



## 一、安装xcdoe命令行
 
```bash
xcode-select --install 
```


## 二、通过PECL来安装PHP扩展

在 `/usr/lib/php` 目录下有个 `install-pear-nozlib.phar` 文件（怎么来的？），通过它首先安装 PECL 管理器

```bash
sudo php install-pear-nozlib.phar
```


## 三、获取所需的扩展
比如，我们安装 xdebug
 
```bash
sudo pecl install xdebug
```

## 四、更新 php 配置文件
打开 `php.ini`，配置新获取的扩展（略）
 
