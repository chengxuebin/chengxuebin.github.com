---
layout: post
title: "url 编码常规"
category: "php"
tags: ["url"]
---


## MVC 框架支持的模式（Pattern）：

- GET 模式： `fumujidi.com/index.php?controller=life&action=books`
- Path Info 模式： `fumujidi.com/index.php/life/books.html`
- SEO 模式： `fumujidi.com/life/books.html`
NODIR 模式： `fumujidi.com/life-books.html`

## 我们使用：

- `SEO` 模式
- 使用连字符规则，不使用大写
- 命名首选 `RESTful` 方式
- 每个尽量不要超过2个单词，多个单词使用 `hyphens（dash）` 格式 

