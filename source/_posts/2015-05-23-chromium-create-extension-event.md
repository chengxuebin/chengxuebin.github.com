---
layout: post
title: "创建 Chromium Extension Event"
category: "chromium"
tags: ["chromium", 'extension']
---



## 一、创建事件名
- 打开 `chrome/browser/extensions/event_names.h`

添加
 
```cpp
namespace event_names {
...

+ extern const char kOnLoginByCertComplete[];
}  
```

- 打开 `chrome/browser/extensions/event_names.cc`

添加
 
```cpp
namespace event_names {
...

+ const char kOnLoginByCertComplete[] = "wintrust.onLoginByCertComplete";

}
```

## 二、添加事件的描述

- 打开 `chrome\common\extensions\api\wintrust.json`

添加
 
```js
[{
    "namespace": "wintrust",
	...

+    "events": [{
+      "name": "onLoginByCertComplete",
+      "description": "Fired when login vpn by cert.",
+      "type": "function",
+      "parameters": [{"name": "success", "type":"boolean"}]
+    }]

}]
```

## 三、添加触发扩展事件的事件
- 打开 `content\public\browser\notification_types.h`

添加
 
```cpp
enum NotificationType {
...

+// 证书登录的结果
+NOTIFICATION_LOGIN_BY_CERT_COMPLETE,

}
```

## 四、绑定**第三步**事件的回调
- 打开 `chrome/browser/extensions/browser_event_router.cc`

添加绑定
 
```cpp
BrowserEventRouter::BrowserEventRouter(Profile* profile)
    : profile_(profile) {
  ...

  registrar_.Add(this,
                 content::NOTIFICATION_LOGIN_BY_CERT_COMPLETE,
                 content::Source<Profile>(profile_));
}
```

实现回调

```cpp
void BrowserEventRouter::Observe(int type,
                                 const content::NotificationSource& source,
                                 const content::NotificationDetails& details) {
  ...
  } else if (type == content::NOTIFICATION_LOGIN_BY_CERT_COMPLETE) {
    scoped_ptr<ListValue> args(new ListValue());
    bool value = *(content::Details<const std::string>(details).ptr()) == "true";
    args->Append(Value::CreateBooleanValue(value));
    DispatchEvent(profile_, events::kOnLoginByCertComplete, args.Pass(), EventRouter::USER_GESTURE_UNKNOWN);
  } else {
    NOTREACHED();
  }
}
```

## 五、实现触发消息

在某个合适和需要的位置以下列的代码触发 `NOTIFICATION_LOGIN_BY_CERT_COMPLETE` 事件

```cpp
+  std::string detail("false");
+  content::NotificationService::current()->Notify(
+      content::NOTIFICATION_LOGIN_BY_CERT_COMPLETE,
+      content::Source<Profile>(browser_->profile()),
+      content::Details<const std::string>(&detail));
```

## 六、测试新事件

1. 在扩展的 `manifest` 里首先声明对 `wintrust` 命名空间的权限
    
    ```js
    "permissions": [
	    "*://*/*",
	    "wintrust"
	]
    ```
    
2. 绑定事件回调函数
    
    ```js
    chrome.wintrust.onLoginByCertComplete.addListener(function(success){
        console.log('Login result is: ', success);  
    })
    
    ```
    
3. 触发**第五步**定义的条件，使事件发生。