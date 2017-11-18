---
layout: post
title: "php 编码常规"
category: "php"
tags: ["php"]
---


## MVC 框架命名规范

### 控制器（controller）

- 类名遵守大写CamelCase，继承`RootController`

```php
class UserController extends RootController   // 类后缀为 Controller
```

- Action名、变量名、方法名采用小写camelCase： 

```php
public function loginAction () {...}    // Action 后缀为 Action
```

- Action 名与 url 的映射关系： 

```php
high-school -> highSchoolAction
```

### 模型（model）

- 类名遵守大写CamelCase，继承`RootModel`

```php
class UserModel entends RootModel    //类后缀为 Model
```

- 类方法和变量遵守小写camelCase

```php
public function calculateScore () {...}
```

### 视图（view）

- 视图模板（tpl）文件路径

``` 
views/<Controller>/<action>.tpl.php
```

- 目录名对应 `Controller`，首字母大写
- 文件名对应 `action`，首字母小写