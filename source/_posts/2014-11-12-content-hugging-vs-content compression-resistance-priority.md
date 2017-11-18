---
layout: post
title: "优先级：hugging vs compression resistance"
category: "ios"
tags: ["ios","autolayout"]
---


开发IOS应用最快速的手段就是自动布局（auto layout）以及它的约束（constraint）机制，下面说几个属性。

### 1.内嵌内容大小（Intrinsic Content Size）

IOS本身依靠一套布局系统（layout system）完成计算各组件大小，最大限度满足尽可能多的约束，说尽可能多的原因是可能存在有冲突的约束，造成没法全部满足。回过头来说，设置了Intrinsic Content Size属性，一是就是通知布局系统本组件包含不容易知道大小的内容，二是会提示布局系统自身的大小，也就是组件本身可以知道自身内容大小。  

常见的组件有UILabel, UIButton, 它们都设置了Intrinsic Content Size这个属性，也就是运行时会根据内容的大小来缩小或扩大。

参见[苹果开发文档](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/AutoLayoutConcepts/AutoLayoutConcepts.html)

### 2.内容保持紧固的优先级（Content hugging priority）

上面说组件会适应其内容大小。那会出现一个问题，例如，当组件还设置了其它约束条件，将左（leading）和右（trailing）边界固定（pinned）到了父视图（superview）上，假如内容宽度是200，父视图宽度是500，那么组件要应用哪个呢？

这时就很有必要为每个条件设置一个优先级，ios默认有高（1000）、中（750）和低（250）3个级别，当然如果需要比较的情况复杂，也可以为优先级设置任意0至1000的数值。

内容保持紧固的优先级指的是组件保持内容大小，**不被拉伸**的优先级别。

回到刚才的例子，将组件的Content hugging priority设置为251，而左右约束设置为250，这时组件会维持自身大小，不会随着父视图拉伸。

### 3.内容抵抗压缩的优先级（content compression resistance priority）

思路与上边相似，本属性指的是组件保持内容大小，**不被压缩**的优先级别。

试想上例中，如果父视图只有宽度只有100，组件内容是200，将组件的content compression resistance priority设置为751，而左右约束设置为750，这时组件会维持自身大小，不会随着父视图被压缩。

### 4. 附上一个讲的很清楚的例子

Say you've got button like this:

**[       Click Me      ]**

and you've pinned the edges to a larger superview with priority 500.

Then, if Hugging priority > 500 it'll look like this:

**[Click Me]**

If Hugging priority < 500 it'll look like this:

**[       Click Me      ]**

If superview now shrinks then, if the Compression Resistance priority > 500, it'll look like this

**[Click Me]**

Else if Compression Resistance priority < 500, it could look like this:

**[Cli..]**

from [stackoverflow](http://stackoverflow.com/questions/15850417/cocoa-autolayout-content-hugging-vs-content-compression-resistance-priority)

