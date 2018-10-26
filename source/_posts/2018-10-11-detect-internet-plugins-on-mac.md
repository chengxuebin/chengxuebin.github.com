---
layout: post
title: "Mac 系统检测浏览器插件"
category: "mac"
tags: ["npplugin", "mac"]
---

### 通过插件版本号检测

```shell
% /usr/libexec/PlistBuddy -c 'Print CFBundleVersion' /Library/Internet\ Plug-Ins/npSecEditCtl.MAC.BOC.plugin/Contents/Info.plist

# 输出结果
03002
```

### 通过遍历插件目录检测

遍历 `/Library/Internet Plug-Ins/` 目录

### 通过 javascript 检测

如果使用 javascript，首选需要借助 safari 的 webbrowser 组件来运行。检测的代码如下：

```javascript
var pluginCount = navigator.plugins.length;
for(var i = 0; i < pluginCount; i++){
  if(navigator.plugins[i].name=="Shockwave Flash") {
    document.write("Flash version " + navigator.plugins[i].version);
    break;
  };
};
```


### 参考

1. [Mac OS X check if flash is installed](https://stackoverflow.com/questions/7603513/mac-os-x-check-if-flash-is-installed)
