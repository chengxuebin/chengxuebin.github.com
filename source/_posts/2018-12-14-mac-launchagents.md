---
layout: post
title: "Mac 系统 LaunchAgents"
category: "mac"
tags: ["mac"]
---

launchctl是一个统一的服务管理框架，可以启动、停止和管理守护进程、应用程序、进程和脚本等。

## LaunchDaemons - 启动时执行

添加本类脚本，需要注意以下几点：

- 放置到目录 /Library/LaunchDaemons/
- 确保文件的用户为 root 并且组为 wheel 
- 拥有权限（permissions） “644”: Owner – read/write, group – read, everyone – read

## LaunchAgents - 登录时执行

- 该类脚本可以放置在以下位置:
    * /Library/LaunchAgents/  – (For all users)
    * ~/Library/LaunchAgents/  – (For a specific user)
    * /System/Library/LaunchAgents/ – (For OS X use only)
- 文件的用户为 root 并且组为 wheel 
    注意: 当放置在 ~/Library/LaunchAgents/ 时， 用户应该为当前拥有 home 目录的用户
- 拥有权限（permissions） “644”: Owner – read/write, group – read, everyone – read

## 操作命令

```shell
# 加载任务
$ launchctl load -w /Library/LaunchAgents/com.onthink.plist 

# 删除任务
$ launchctl unload -w /Library/LaunchAgents/com.onthink.plist 

# 查看任务列表
$ launchctl list | grep 'com.onthink'
```

## 参考

1. [Spring Boot干货系列：（一）优雅的入门篇](http://tengj.top/2017/02/26/springboot1/)