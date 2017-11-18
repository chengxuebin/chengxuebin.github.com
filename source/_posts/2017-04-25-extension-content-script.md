---
layout: post
title: "Chrome 扩展之： Content Script"
category: "chrome"
tags: ["chrome","extension"]
---



## 1 介绍


## 1.1 使用场景

`Content Script` 是一个很神奇的东西。它可以读取和修改页面内容。比如一些应用场景

* 过滤广告：将页面中的广告区块过滤掉，只显示主要内容
* 翻译页面中的外语
* 分享页面内容到社交平台


### 1.2 限制

* Use chrome.* APIs, with the exception of:
    * extension ( getURL , inIncognitoContext , lastError , onRequest , sendRequest )
    * i18n
    * runtime ( connect , getManifest , getURL , id , onConnect , onMessage , sendMessage )
    * storage
* 不能使用扩展页面中的变量和方法
* 不能使用定义在其它扩展的 Content Script 中的变量和方法


## 2 使用

### 2.1 Manifest

```javascript
{
  "name": "My extension",
  ...
  "content_scripts": [
    {
      "matches": ["http://www.google.com/*"],
      "css": ["mystyles.css"],
      "js": ["jquery.js", "myscript.js"]
    }
  ],
  ...
  "permissions": [
    "tabs", "http://www.google.com/*"
  ],
  ...
}
```

### 2.2 注入


#### 2.2.1 与 `browserAction` 配合

注入并执行一段代码：

```javascript
chrome.browserAction.onClicked.addListener(function(tab) {
  chrome.tabs.executeScript({
    code: 'document.body.style.backgroundColor="red"'
  });
});
```

执行上面的代码先获取权限：

```javascript
"permissions": [
  "activeTab"
],
```


或直接执行一个文件：


```javascript
chrome.tabs.executeScript(null, {file: "content_script.js"});
```

`blocking` 会将回调函数设置为同步处理，请求会等待回调完成。使用该参数需要在 `permission` 中添加 `webRequestBlocking` 权限。

### 2.2.2 获取资源文件 URL

```javascript
/Code for displaying <extensionDir>/images/myimage.png:
var imgURL = chrome.extension.getURL("images/myimage.png");
document.getElementById("someImage").src = imgURL;
```


## 3 要点

### 3.1 ContentScript 在独立环境运行


### 3.2 注入的方式

#### 3.2.1 整个文件

```javascript
var s = document.createElement('script');
// TODO: add "script.js" to web_accessible_resources in manifest.json
s.src = chrome.extension.getURL('script.js');
s.onload = function() {
    this.remove(); // 执行完后清理注入标签
};
(document.head || document.documentElement).appendChild(s);
```

**注意：** 将 `script.js` 加入到 ["web_accessible_resources"](https://developer.chrome.com/extensions/manifest/web_accessible_resources)

#### 3.2.2 代码片段

```javascript
var actualCode = `// Code here.
// If you want to use a variable, use $ and curly braces.
// For example, to use a fixed random number:
var someFixedRandomValue = ${ Math.random() };
// NOTE: Do not insert unsafe variables in this way, see below
// at "Dynamic values in the injected code"
`;

var script = document.createElement('script');
script.textContent = actualCode;
(document.head||document.documentElement).appendChild(script);
script.remove();
```


#### 3.2.3 更多方式

参见： [Insert code into the page context using a content script](http://stackoverflow.com/questions/9515704/insert-code-into-the-page-context-using-a-content-script/9517879#9517879)


### 3.3 注入的时机


#### 3.3.1 document_start

* 在 `document.readyState` 事件前被触发。
* document.head 和 document.body 还不可用，只有 documentElement
    ```javascript
    var s = document.createElement ("script");
    s.src = "http://ajax.googleapis.com/ajax/libs/jquery/2.1.0/jquery.min.js";
    s.async = false;
    document.documentElement.appendChild (s);
    ```

    ```javascript
    var s = document.createElement ("script");
    s.src = chrome.extension.getURL ("MyPwnCode.js");
    s.async = false;
    document.documentElement.appendChild (s);
    ```
* 如果需要操作其它 DOM，可以等待 `DOMContentLoaded` 事件
    ```javascript
    document.addEventListener('DOMContentLoaded', fireContentLoadedEvent, false);

    function fireContentLoadedEvent () {
        console.log ("DOMContentLoaded");
        // PUT YOUR CODE HERE.
        //document.body.textContent = "Changed this!";
    }
    ```


#### 3.3.2 document_end

* DOM 已解析完成
* 资源如 image 和 frame 还没有加载（onload）


#### 3.3.3 document_idle

* 晚于 document_end，
* 也晚于 window.onload
* DOM 已解析完成

