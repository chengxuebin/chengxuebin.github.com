---
layout: post
title: "使用 Flyway"
category: "java"
tags: ['java', 'SpringMVC']
---

[Flyway](https://flywaydb.org/) 是一款简单好用的数据库迁移工具。优点是简洁、易上手，功能满足常规开发、维护，并提供了命令行工具。


### 安装命令行工具

通过 `homebrew` 安装非常方便：

```shell
$ brew install flyway
```

### 配置

![](https://flywaydb.org/assets/balsamiq/CommandLineDirectoryStructure.png)

默认安装的配置文件保存在：`/usr/local/Cellar/flyway/4.2.0/libexec/conf/flyway.conf`，其中包含非常详细的注释。

由于需要在多个项目中使用 flyway，每个项目的配置又不完全相同，所以修改默认配置不是我需要的方案。于是，我在每个项目中分别保存一份配置文件，执行时加载各自的配置。

**项目配置文件**

```ini
# file: flyway.conf

# 数据库连接
flyway.url=jdbc:mysql://localhost:3306/database
flyway.user=user
flyway.password=password

# 迁移脚本的路径，对应：项目根目录/sql
flyway.locations=filesystem:sql
```

<!-- more -->

### 迁移脚本

关于脚本的几个重要点：

* 脚本可以用 java 或 sql 写
* 每个文件以版本号开头: `V1__`
* 迁移时按照版本号顺序执行


### 命令

#### 1. 迁移

![](https://flywaydb.org/assets/balsamiq/command-migrate.png)

```shell
$ flyway -configFiles=./flyway.conf migrate
```

