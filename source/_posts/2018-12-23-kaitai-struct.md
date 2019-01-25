---
layout: post
title: "学习使用 kaitai 结构"
category: "tools"
tags: ["nodejs","c++"]
---


kaitai 是一个开源的用于解析二进制文件的解析器，包含了编译器、IDE 和一系列常用的二进制格式描述脚本，你可以直接利用这些脚本去解析对应的二进制文件。

### 它支持的语言：

* C++/STL
* C#
* Go (*)
* Java
* JavaScript
* Lua
* Perl
* PHP
* Python
* Ruby


<!-- more -->

### 安装编译工具

通过命令行安装 [compiler](https://github.com/kaitai-io/kaitai_struct_compiler)：

```shell
% brew install kaitai-struct-compiler
```

### 使用举例：解析 `.gif` 文件

#### 1 编写描述脚本

描述脚本以 `.ksy` 为扩展名，语法参考[这里](https://doc.kaitai.io/ksy_reference.html)，对于同一个二进制文件，由于关注的数据重点不相同，**描述可以有很多种**，下面是 `.gif` 的描述：

```c++
meta:
  id: gif
  file-extension: gif
  endian: le
seq:
  - id: header
    type: header
  - id: logical_screen
    type: logical_screen
types:
  header:
    seq:
      - id: magic
        contents: 'GIF'
      - id: version
        size: 3
  logical_screen:
    seq:
      - id: image_width
        type: u2
      - id: image_height
        type: u2
      - id: flags
        type: u1
      - id: bg_color_index
        type: u1
      - id: pixel_aspect_ratio
        type: u1
```

#### 2 编译成目标语言

本示例使用 javascript，

```shell
% ksc -t javascript Gif.ksy
```

#### 3 编写调用

```js
// index.js
const fs = require("fs");
const KaitaiStream = require('kaitai-struct/KaitaiStream');
const Gif = require('./fomulas/Gif.js');
const arrayBuffer = fs.readFileSync('./a-gif-file.gif', null);
const g = new Gif(new KaitaiStream(arrayBuffer));
console.log("width = " + g.logicalScreen().imageWidth());
console.log("height = " + g.logicalScreen().imageHeight());
```

通过命令行执行：

```shell
% nodejs index.js
```

查看终端输出结果，已经从 gif 文件中获取图像的宽和高值。

### 更多“配方”

官网提供了众多已经定义好的“配方”供大家参考，请看[这里](http://formats.kaitai.io/)

