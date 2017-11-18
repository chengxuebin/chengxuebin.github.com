---
layout: post
title: "常用的正则表达式"
category: "program"
tags: ["regexp"]
---


## 常用表达式

### 1. 中文字符

```javascript
/[\u4e00-\u9fa5]/
```

### 2. 邮箱

```javascript
/^([a-z0-9\+_\-]+)(\.[a-z0-9\+_\-]+)*@([_a-z0-9]+\.)+[a-z]{2,5}$/
```

### 3. 多个单词或

```javascript
/(?:apple|banna)/
```

### 4. 不包含某个单词

```javascript
/^((?!hede).)*$/
```

参考：

* [Exclude a String](http://fineonly.com/solutions/regex-exclude-a-string)
* [正则表达式里字符串”不包含”匹配技巧](http://www.vaikan.com/regular-expression-to-match-string-not-containing-a-word/)
* [A regular expression to exclude a word/string](http://stackoverflow.com/questions/2078915/a-regular-expression-to-exclude-a-word-string)

### 5. 神奇的全部 ASCII 字符

```javascript
/[ -~]/
/[\x20-\x7E]/
```

**解释**：[ASCII 表](http://www.asciitable.com/)中大于空格（0x20），小于~（0x7E）的字符为全部的**打印**字符。


### 6. 处理 HTML 标签

参考：

* [Using a Regular Expression to Match HTML](http://haacked.com/archive/2004/10/25/usingregularexpressionstomatchhtml.aspx/)
* [All about HTML tags](http://www.pagecolumn.com/tool/all_about_html_tags.htm)

### 7. 使用空格分隔中文和英文单词

```javascript
([\x{4E00}-\x{9FA5}])([a-z0-9@#&;=_\[\$\%\^\*\-\+\(\/]+)

$1 $2
```


## JavaScript

Modifier | Description
:---: | :---
i | Perform case-insensitive matching
g | Perform a global match (find all matches rather than stopping after the first match)
m | Perform multiline matching



收集了一些总结的很好的文章：

* [最全的常用正则表达式大全](http://www.cnblogs.com/zxin/archive/2013/01/26/2877765.html)
* [regular-expressions.info](http://www.regular-expressions.info/lookaround.html)
* [RegExr](http://regexr.com/30npe)
