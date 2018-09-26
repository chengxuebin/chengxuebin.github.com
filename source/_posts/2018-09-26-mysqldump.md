---
layout: post
title: "mysqldump 用法"
category: "database"
tags: ["MySQL", "database"]
---

### 只导出表结构

```shell
# 只导出表结构
mysqldump --no-create-info -h localhost -u root -p jizhunmall_test > ~/zhp_20180925_data.sql
```

### 只导出数据

```shell
# 只导出数据
mysqldump -d -h localhost -u root -p jizhunmall_test > ~/zhp_20180925_tables.sql
```

<!-- more -->





