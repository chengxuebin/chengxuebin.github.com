---
layout: post
title: "IOS要点总结"
category: "ios"
tags: ["ios"]
---


总结ios开发遇到的易混知识点、陷阱

## Frame VS Bounds
A picture worth thousand words! 见下图。

- **Frame**： 视图的frame是相对父坐标系的原点和矩形，默认从左上点开始。

- **Bounds**： 视图的bounds是相对自身坐标系的原点和区域。

- **Center**： 中点是相对父坐标系的中心点。 注意**middle**是相对自身坐标系的中心点

注意，当view旋转（rotate）时：

```obj-c
frame.size != bounds.size
```

![The Kitchen Drawer based on Stanford CS193p course](http://i.stack.imgur.com/3jcne.jpg)

## initWithFrame VS initWithCoder VS awakeFromNib

- initWithFrame 是用户主动调用的，传入用来初始化的frame
- initWithCoder 是被触发的。当UIView被反序列化（deserialize）后，被触发。
- awakeFromNib 是被触发的。当UIView与xib内组件建立好连接（connection）后被触发。

注：initWithNibName:boundle是发送到UIViewController的消息，与上面的方法对象不同。

![](/images/posts/view-init.png)

## extension VS category

- category是一种扩展已经存在的Class的方法。当你不能接触到Class的源码时，这是一种很有用的方式. 它常把文件取名为 "Class+CategoryName.h", 比如 "NSView+CustomAdditions.h"， 包括.m的命名.

- extension是一种特殊的category, 有以下两个不同：
	- 没有被扩展类名的category. 它的声明像这样:
	
	```obj-c
	@interface SomeClass ()
		- (void) anAdditionalMethod;
	@end
	```

	- extension常定义在实现（.m）文件的顶部，然后在其下@implementation部分实现它。这也成为一种实现“伪私有”（pseudo-private）成员的方式。伪私有顾名思义，并不是真的声明了私有成员，只是达到了不能在外部访问的目的。
