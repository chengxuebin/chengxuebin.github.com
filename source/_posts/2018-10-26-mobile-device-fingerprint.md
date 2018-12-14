---
layout: post
title: "移动设备特征值"
category: "mobile"
tags: ["ios", "android"]
---

## iOS 设备

### UDID - iOS Unique Device ID

* 设备的唯一识别符，移动广告商和游戏网络运营商往往需要通过UDID用来识别玩家用户，并对用户活动进行跟踪。
* 小于 iOS5.0
* **不再可用**

### Device Token

* 用于向 iOS 设备推送消息的标识（Apple Push Notification System）。每个 App 有两个标识，一个用于开发推送，一个用于生产推送（ad hoc or app store builds）
* iOS7 及以后
* 64位十六进制序列（hexadecimal characters）
* 往另一台设备恢复数据或重装系统（抹掉系统数据），会造成 device token 改变，所以不能用它来唯一标识一台设备。参考[这里](https://stackoverflow.com/questions/40169404/does-a-ios-push-notification-device-token-change/40169657)

### UUID - Universally Unique Identifier

* 通用唯一识别码，它保证每台机器都是唯一的。每次生成都会不一样，所以第一次启动获取后，存储在钥匙串Keychain中，这样app被删除了，Keychain里的数据依然存在，除非系统重置
* 32位的十六进制序列
* 抹掉或重装系统会造成改变

### IDFA — Identifier For Advertisers

* 广告标示符，苹果专门给各广告提供商用来追踪用户而设的标识
* iOS6及之后
* iOS10 后，苹果推出了“限制广告追踪”功能，参考[这里](https://mobiledevmemo.com/idfa-zeroing-ios-10-change-mobile-advertising/)，用户可以在iPhone 设置中选择关掉 IDFA 获取权限。

### IDFV — Identifier For Vendors

* 运营商标示符，来自同一个运营商的应用运行在同一个设备上，此ID的值是相同的，不同的运营商应用在同一个设备上的值不同。
* iOS6 及之后
* 如果同一运营商的 app 全部删除，重新安装后的ID值会改变，参考[这里](https://stackoverflow.com/questions/48153518/does-identifierforvendor-change-on-app-update)

### MAC

* iOS7 后禁止使用。返回同一值：`02:00:00:00:00:00`

### IMEI - International Mobile Equipment Identity

* 苹果不允许获取 IMEI

## Android 设备

### IMEI - International Mobile Equipment Identity

* 国际移动设备身份码的缩写，国际移动装备辨识码，是由15位数字组成的"电子串号"，它与每台手机一一对应，而且该码是全世界唯一的
* 15位
* 用户设置权限时禁止，将获取不到


### Mac

* android 6.0 以后，googel官方为了加强权限管理而禁用了getSYstemService，不管任何手机都会返回”02:00:00:00:00:00”这个默认的mac地址


### ANDROID_ID

* 在设备首次运行的时候，系统会随机生成一64位的数字，并把这个数值以16进制保存下来，这个16进制的数字就是ANDROID_ID，但是如果手机恢复出厂设置这个值会发生改变。
* 手机恢复出厂设置以后该值会发生变化
* 在国内Android定制的大环境下，有些设备是不会返回ANDROID_ID的


### SN - Serial Number

* 设备序列号
* 获取序列号不需要权限，但是有一定的局限性，在有些手机上会出现垃圾数据，比如红米手机返回的就是连续的非随机数



### 参考

1. [如何唯一的标识一台Android设备？](https://www.jianshu.com/p/178786f833b6)
