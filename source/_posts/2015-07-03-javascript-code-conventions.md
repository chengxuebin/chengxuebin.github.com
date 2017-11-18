---
layout: post
title: "javascript 编码常规"
category: "javascript"
tags: ["javascript"]
---


标题写成常规，非规范，是由于只要没有语法错误，如何写代码本身就是编码者的一个喜好而已，但是好的习惯有助于提高可读性，减少错误，尤其提高团队合作的效率。

下面内容搜集了业内顶级开发者的习惯，来源见底部附录。

## JavaScript 文件
所有 javascript 文件根据依赖关系，按序放置在 `<body>` 结束标签前。  
原因：  
- 不会因为 javascript 文件的加载和解析延缓页面显示
- 防止标签在加载完成前被 js 代码调用。

## 缩进 Indentation
- 使用空格代替 `tab`
- 缩进单位是2个空格

## 代码行
- 每行不超过80字符
- 换行缩进8个空格

## 注释 Comment
- 尽量多地注释代码的思路、注意点等
- 修改代码时同步更新注释

## 命名
- 使用26大写和小写字母`(A .. Z, a .. z)`，10个数字`(0 .. 9)`，下划线`(_)`。不要使用 `$ (dollar sign)` 或 `\ (backslash)`
- 首尾不使用下划线
- 变量或方法遵守驼峰（camelCase）规则
- 构造函数遵守大写驼峰规则
- 全局变量遵守全大写规则

```javascript
var MESSAGE_TYPE_BROADCAST = 0,    // 全局变量
    MESSAGE_TYPE_PERSON = 1,
    MESSAGE_TYPE_GROUP = 2;

function sendMessage(messageType, message){    // 方法名
    
    var httpStatus,
        sendResult;     // 变量
    
    // detect message target by message type
    ... 
    
    httpStatus = httpRequest(message);
    
    // handle result
    ...
   
    return sendResult;
}
```



## 变量 Variable
- 所有变量在使用前声明，`var` 应该是 `Function` 第一行语句
- 变量及其注释单独一行
- 变量名遵守驼峰（camelCase）规则

    ```javascript
        var currentEntry, // currently selected table entry
        level,        // indentation level
        size;         // size of table
    ```

## 方法 Function
- 方法名遵守驼峰（camelCase）规则
- 立即执行的方法使用小括号封装

    ```javascript
    var collection = (function () {
        var keys = [], values = [];
    
        return {
            get: function (key) {
                var at = keys.indexOf(key);
                if (at >= 0) {
                    return values[at];
                }
            },
            set: function (key, value) {
                var at = keys.indexOf(key);
                if (at < 0) {
                    at = keys.length;
                }
                keys[at] = key;
                values[at] = value;
            },
            remove: function (key) {
                var at = keys.indexOf(key);
                if (at >= 0) {
                    keys.splice(at, 1);
                    values.splice(at, 1);
                }
            }
        };
    }());
    ```
    
## 格式示例

### if 语句（Statement）


```javascript
    if (condition) {
        statements
    }
    
    if (condition) {
        statements
    } else {
        statements
    }
    
    if (condition) {
        statements
    } else if (condition) {
        statements
    } else {
        statements
    }
```

### for 语句


```javascript
    for (initialization; condition; update) {
        statements
    }

    for (variable in object) {
        if (filter) {
            statements
        } 
    }
```


### while 语句

```javascript
    while (condition) {
        statements
    }
```

### do 语句

```javascript
    do {
        statements
    } while (condition);
```

Unlike the other compound statements, the do statement always ends with a ; (semicolon).

### switch 语句

```javascript

    switch (expression) {
    case expression:
        statements
    default:
        statements
    }
```

每一组必须有结束代码：`break, return, or throw`


### try 语句

```javascript

    try {
        statements
    } catch (variable) {
        statements
    }

    try {
        statements
    } catch (variable) {
        statements
   } finally {
        statements
   }
```

### continue 语句

**不要使用**

### with 语句

**不要使用**

### eval 语句

**不要使用**

## 参考：
*[Javascript编程风格](http://www.ruanyifeng.com/blog/2012/04/javascript_programming_style.html)*  
*[Code Conventions for the JavaScript Programming Language](http://javascript.crockford.com/code.html)*  
*[Google JavaScript Style Guide](http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml)*
 

