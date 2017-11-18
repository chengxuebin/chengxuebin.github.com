---
layout: post
title: "PHPStorm 设置 watcher"
category: "php"
tags: ["php","ide"]
---


#### 步骤

![](/images/posts/phpstorm_watch/pic-1.png)

![](/images/posts/phpstorm_watch/pic-2.png)

#### Arguments:

```shell
--no-cache --update $FileName$:$ProjectFileDir$/dist/css/$FileNameWithoutExtension$.css
```

#### Outputs path to refresh:

```shell
$ProjectFileDir$/dist/css/$FileNameWithoutExtension$.css:$ProjectFileDir$/dist/css/$FileNameWithoutExtension$.css.map
```


