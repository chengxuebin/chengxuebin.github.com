---
layout: post
title: "指定 IntelliJ 使用的 server.xml"
category: "java"
tags: ['java']
---


### 打开 IntelliJ 的配置

进入 `Startup/Connection` 标签页

![](/images/posts/intellij_config/config-1.png)

### 编辑 `Startup Script`

取消选择 `Use Default`，点击编辑按钮，进入参数编辑窗口


![](/images/posts/intellij_config/config-2.png)

### 添加 `Program Arguments`

在 `run` 后添加配置文件的地址：

```shell
run -config /usr/local/apache-tomcat-8.0.26/conf/server.xml
```

<!-- more -->

### 添加 `VM Options`

在 `VM Options` 后添加配置文件的地址：

```shell
-Dcom.sun.management.jmxremote.port=1099
-Dcom.sun.management.jmxremote=
-Dcom.sun.management.jmxremote.ssl=false
-Dcom.sun.management.jmxremote.authenticate=false
-Djava.rmi.server.hostname=127.0.0.1
```

![](/images/posts/intellij_config/config-3.png)
