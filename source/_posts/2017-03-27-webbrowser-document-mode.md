---
layout: post
title: "设置 WebBrowser Document Mode"
category: "ie"
tags: ["c++","ie","windows"]
---




### 编辑注册表


```shell
HKEY_CURRENT_USER\SOFTWARE\Microsoft\Internet Explorer\Main\FeatureControl\FEATURE_BROWSER_EMULATION
```



### 设置值

| 值 | 说明 |
|:--- |:--- |
|10001 (0x2711)  | Internet Explorer 10。网页以IE 10的标准模式展现，页面!DOCTYPE无效 |
|10000 (0x02710)  | Internet Explorer 10。在IE 10标准模式中按照网页上!DOCTYPE指令来显示网页。Internet Explorer 10 默认值。 |
|9999 (0x270F) |  Windows Internet Explorer 9. 强制IE9显示，忽略!DOCTYPE指令 |
|9000 (0x2328)  | Internet Explorer 9. Internet Explorer 9默认值，在IE9标准模式中按照网页上!DOCTYPE指令来显示网页。 |
|8888 (0x22B8)  | Internet Explorer 8，强制IE8标准模式显示，忽略!DOCTYPE指令 |
|8000 (0x1F40)  | Internet Explorer 8默认设置，在IE8标准模式中按照网页上!DOCTYPE指令展示网页 |
|7000 (0x1B58)  | 使用WebBrowser Control控件的应用程序所使用的默认值，在IE7标准模式中按照网页上!DOCTYPE指令来展示网页。 |


### 举例：

![](/images/posts/ie/set-reg.png)





