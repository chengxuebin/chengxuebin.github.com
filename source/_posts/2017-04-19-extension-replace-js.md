---
layout: post
title: "Chrome 扩展之： 使用 webRequest 替换页面资源"
category: "chrome"
tags: ["chrome","extension"]
---




## 请求事件

首先，一个请求的生命周期是这样的：

![](/images/posts/chrome_extension/webrequestapi.png)

利用 [webRequest](https://developer.chrome.com/extensions/webRequest) 接口，可以监听图中的事件，插入自己的处理。


## 知识点：

### 1. webRequest 需要在 background 中使用

### 2. 调用方式

```javascript
var callback = function(details) {...};
var filter = {...};
var opt_extraInfoSpec = [...];

chrome.webRequest.onBeforeRequest.addListener(
    callback, filter, opt_extraInfoSpec);
```

### 3. `blocking` 参数

`blocking` 会将回调函数设置为同步处理，请求会等待回调完成。使用该参数需要在 `permission` 中添加 `webRequestBlocking` 权限。

## 举例： 替换网站的 JS 文件

### 1. 声明权限 

编辑 `manifest.json`，添加 `permission`

```json
"permissions": [
  "webRequest",
  "webRequestBlocking",
  "http://*/*",
  "https://*/*"
]
```

### 2. 添加后端 文件

首先在 `manifest.json` 中添加 background 文件

```json
"background": {
  "scripts": ["dist/js/background.js"],
  "persistent": true
}
```


### 3. webRequest 脚本

```javascript
chrome.webRequest.onBeforeRequest.addListener(
  function(details) {
    if( details.url.endsWith('origin.js') ){
      return {redirectUrl: chrome.extension.getURL ("dist/bake/fake.js")};
    }
  },
  {urls: ["*://*/*.js"]},
  ["blocking"]
);
```


解释一下上面的后端代码：

* 监听 **符合规定请求** 的 `onBeforeRequest` 事件
* 这2个参数是回调函数，通过参数 `details` 来判断每一个请求 `url`，如果以 `origin.js` 结尾，将用扩展中准备好的 `fake.js` 替换。换句话说，将原始网站的 JS 替换为假的 JS
* 对于用户来讲，通过查看源码，看到的仍然是 `<script src="origin.js"></script>`
* 通过 DevTools 的 network 查看，会发现 `url` 已经变为 `fake.js`
* 第3个参数过滤被监听的请求，支持通配符 `*`
* 第4个参数为扩展参数，`blocking` 说明这是 **同步** 处理，请求会等待回调函数完成








