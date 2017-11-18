---
layout: post
title: "IntelliJ，Tomcat，Maven，Spring"
category: "java"
tags: ["java","server","mac"]
---



### 常见问题

#### 1）IntelliJ 部署目录是不是 Tomcat webapps 目录？

IntelliJ 默认发布到 `${dir.to.idea.project}\YourWebApp\out\artifacts`，而不是 Tomcat webapps 目录。但是可以通过配置，将配置文件发现到 Tomcat 目录：

![配置发布tomcat](http://i.stack.imgur.com/yGOph.png)

```shell

```


