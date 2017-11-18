---
layout: post
title: "XCode 5 添加复制/删除行快捷键"
category: "ios"
tags: ["ios","xcode"]
---


习惯了某些编辑器的复制一行和删除一行快捷方式后，使用xcode时没有相应快捷键有些不适应，下面是一种为其添加快捷键的方式。

### 修改快捷键配置文件的权限

```bash
sudo chmod 666 /Applications/Xcode.app/Contents/Frameworks/IDEKit.framework/Resources/IDETextKeyBindingSet.plist

sudo chmod 777 /Applications/Xcode.app/Contents/Frameworks/IDEKit.framework/Resources/
```

![](/images/posts/duplicate_delete_line_2.png)

### 修改快捷键配置文件

1. 添加Customized类别
1. 添加Delete Current Line宏（macro）  

	```bash
	selectLine:, cut:
	```

1. 添加Duplicate Current Line宏  

	```bash
	selectLine:, copy:, moveToEndOfLine:, insertNewline:, paste:, deleteBackward:  
	```

&nbsp;
![](/images/posts/duplicate_delete_line_1.png)

### 绑定快捷键
重启 Xcode，打开菜单 `Xcode > Preferences > KeyBindings`。 找到上面定义的宏，为其定义快捷键。

![](/images/posts/duplicate_delete_line_3.png)