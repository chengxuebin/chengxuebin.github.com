---
layout: post
title: "配置 Zend Studio, Xdebug"
category: "php"
tags: ["php","xdebug", "zend studio"]
---


开发 PHP 过程中，经常不是一蹴而就的，某些疑难问题需要像本地程序一样调试。本文记录如何配置 Zend Studio 和 Xdebug，实现本地调试或远程（Remote）调试。

说到本地或远程调试，就要涉及到 PHP 的2种运行方式：CLI 和 CGI。

### TIPS：什么是 CLI 和 CGI？  
- CLI（Command Line Interface），即“命令行界面”，是指在命令行界面的运行方式。比如在本地执行一个 PHP 脚本。
- CGI（Common Gateway Interface），即“通用网关接口”，是 HTTP 服务器和脚本解释器之间的接口。通过这个通用接口，可以忽略 HTTP 服务器或脚本解释器的技术限制。HTTP 服务器常用的就是 Apache，Lighttpd，Nginx，Tomcat等，脚本解释器常用的有 PHP，ASP，JSP 等解释器。


## 安装 Xdebug

### A. 安装到远程服务器

1. 下载 Xdebug 到远程服务器

1. 修改 php.ini  
	打开 php.ini，添加 Xdebug 扩展（dll）的路径
	```
	Linux and Mac OS: zend_extension="/usr/local/php/modules/xdebug.so"
	Windows: zend_extension="<path to .dll file>"
```

1. 配置 Xdebug
	在 php.ini 中，添加配置，其中关键的几个是：`remote_enable`，`remote_autostart`，`remote_port`，`remote_handler` 
 
	```ini
	xdebug.auto_trace= "On"
	xdebug.collect_params= "On"
	xdebug.collect_return= "On"
	xdebug.trace_output_dir="/path/to/log/dir/xdebug/trace"
	xdebug.profiler_enable= "true"
	xdebug.profiler_output_dir="/path/to/log/dir/xdebug/profiler"
	xdebug.profiler_append = "On"
	xdebug.profiler_enable_trigger = "On"
	xdebug.profiler_output_name = "cachegrind.out.%t-%s"
	xdebug.remote_enable = "On" 
	xdebug.remote_autostart = 0  
	xdebug.remote_host = 127.0.0.1
	xdebug.remote_port = 9000 
	xdebug.remote_handler = "dbgp"
	xdebug.remote_log="/path/to/log/dir/remote.log"
	```  

1. 验证安装是否成功
	远程打印`<?php phpinfo(); ?>`，查看是否安装了 Xdebug 模块。

### B. 安装到 Zend Studio 运行环境（PHP Executable）

Zend Studio 自带了 PHP 运行环境的插件，在 `<Zend_Studio_install_dir>\plugins\com.zend.php.debug.debugger.<version_info>\` 目录里。默认配置的是使用自家的 zend debug，切换成 xdebug，需要修改这里的 php 设置。

1. 下载 Xdebug 到 `<Zend_Studio_install_dir>\plugins\com.zend.php.debug.debugger.<version_info>\`

1. 修改 php.ini  
	注释掉 zend debug，添加 Xdebug，写法和上节一样。

1. 配置 Xdebug
	配置参数参见上节

1. 验证安装是否成功
	打开命令行，执行`php --re xdebug`，如果输出不是`Exception: Extension xdebug does not exist`, 表示 Xdebug 扩展安装成功。


到这里，已经完成了配置 Xdebug 部分，其实现在已经可以使用多种工具来远程或本地调试了，包括设置断点、单步、查看运行参数等。反映较好的调式客户端有 PhpStorm，eclipse+PDT，netbeans，Notepad++等，Zend Studio 也算是 “eclipse+插件”性质的，下面将描述它的配置。顺道说一下，还有一个极好用的工具，是 Chrome 的插件 WebGDBp，可以直接在浏览器里远程调试，非常 Cool。

## 配置 Zend Studio

### A. 配置 Xdebug CLI 调试

1. 打开 `Window | Preferences | PHP | PHP Executables`  
	![](/images/posts/zend_xdebug/cli_1.jpg)

1. 打开 `Add` ，添加 PHP 运行环境  
	![](/images/posts/zend_xdebug/cli_2.jpg)
  - Name - PHP 运行环境名（比如 PHP 5.5.7 with Xdebug）
  - Executable path - 运行环境路径.
  - PHP ini file (Optional) - 运行环境对应的 php.ini 路径
  - SAPI type - 设置为 CLI
  - PHP debugger - 设置为 Xdebug.

1. 设置 Xdebug 为默认运行环境  
	上一步保存后的回到运行环境设置窗口，选择添加的运行环境，点击 `Set as Default `

1. 打开 `Window | Preferences | PHP | Debug`  
	![](/images/posts/zend_xdebug/cli_3.jpg)
	在 CLI 设置区，选择 Xdebug

到此，CLI 使用 Xdebug 已经配置完成。

### B. 配置 Xdebug 远程调试

允许远程调试网站程序（Web Application Debugging）

1. 打开 `Window | Preferences | PHP | PHP Servers`  
	![](/images/posts/zend_xdebug/remote_1.jpg)  

1. 添加服务器（Server） 
	![](/images/posts/zend_xdebug/remote_2.jpg)  

1. 配置路径映射（Path Mapping）
	![](/images/posts/zend_xdebug/remote_3.jpg)  

1. 配置 DBGp 协议
	由于是远程调试，使用了 DBGp 协议通信，需要配置通信参数。
	打开 `Window | Preferences | PHP | Debug | Installed Debuggers` 
	![](/images/posts/zend_xdebug/remote_6.jpg)  
	编辑 Xdebug  
	![](/images/posts/zend_xdebug/remote_7.jpg)  
	这里非常关键的地方是 `Access reomte session (JIT)`，它能配置触发 Zend Studio 调试的条件。当我们从浏览器里访问要调试的网站时，可以选择：
	- `off`: 关闭远程调试功能
	- `localhost`: 通过 localhost 访问时触发调试
	- `any`: 任何访问都触发调试
	- `prompt`: 任何访问弹出提示，选择是否触发调试
	由于我一般都是内网调试，这里选择 localhost，然后配合网站 Xdebug 的配置 `xdebug.remote_autostart = 0` 来使用，只有当访问 localhost 时才触发 Zend Studio 进入调试状态。

1. 开始调试
	（可选）为项目指定默认服务器  
	![](/images/posts/zend_xdebug/remote_8.jpg)  
	现在 Xdebug 已经是这个项目的默认远程 PHP 调试器。你可以通过 `Debug as web application` 来调试某个文件。  
	![](/images/posts/zend_xdebug/remote_4.jpg)  
	查看结果：  
	![](/images/posts/zend_xdebug/remote_5.jpg)  
