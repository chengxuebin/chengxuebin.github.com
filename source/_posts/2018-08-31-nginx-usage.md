---
layout: post
title: "Nginx 配置"
category: "server"
tags: ["server", "nginx"]
---

### 变量

查看 [官方文档-变量](http://nginx.org/en/docs/varindex.html)

<!-- more -->

### try_files

```
语法:  try_files file ... uri;
      try_files file ... =code;
默认值:  无
上下文:  server, location
```

try_files 的作用是按照从左向右顺序尝试每个资源，直到有可用资源处理，作用域是在当前上下文。尝试的资源相对于 `root` 和 `alias` 目录。


**示例1：按顺序尝试，直到返回 404**

```
location / {
    try_files $uri $uri/index.html $uri.html =404;
}
```


**示例2：使用代理**

```
location / {
    try_files /system/maintenance.html
              $uri $uri/index.html $uri.html
              @mongrel;
}

location @mongrel {
    proxy_pass http://mongrel;
}
```


### rewrite

```
语法: rewrite regex replacement [flag];
默认值:  —
上下评论:  server, location, if
```

如果正则匹配 URI，则使用后面的 replacement 完成替换。flag 能决定是否进一步处理。


#### flag 取值

* last
    停止处理当前的 `ngx_http_rewrite_module` 规则集，并对更新的 URI 启动新一轮 location 搜索；
* break
    停止处理当前的 `ngx_http_rewrite_module` 规则集，如同 break 规则；
* redirect
    返回一个 302 临时重定向。如果替换字符串以 `http://`，`https://` 开头或者是 `$scheme` 时使用；
* permanent
    返回一个 301 永久重定向。

如果正则表达式包含 “}” 或 “;” 则整个表达式应该用单引号或双引号包裹。

如果替换字符串中包含新的请求参数，默认之前的请求参数会自动扩充到后面。如果不希望如此，则需要在替换字符串后加一个问号，如下：

```
rewrite ^/users/(.*)$ /show?user=$1? last;
```
