---
layout: post
title: "发布到 App Store"
category: "ios"
tags: ["ios", 'app', ]
---



##	一 一些名词

### App ID：应用的标识

- 格式：上由2部分组成： `<Bundle Seed ID>.<Bundle Identifier>`，例如，`ABCDE12345.com.foocompany.appname`:
	- `ABCDE12345` 是 Bundle Seed ID (苹果生成)
	- `com.foocompany.appname` is the App ID's Bundle Identifier and needs to be the same as your app’s Bundle Identifier

- 分类： 通配符方式（wildcard） VS 显示定义（explicit）
	- 通配符方式： 一般的ID都应该用此方式，理由是一个 Wildcard App ID 可用于编译和安装多个 App。注意星要放到最后
	`Example App ID: ABCDE12345.com.foocompany.*`
	- 显示定义：只能用于一个 App，但是，如果你的应用需要诸如 In App Purchase and Apple Push Notification service 的功能，你就必须使用显示定义
	`Example App ID: ABCDE12345.com.foocompany.appname`

扩展阅读：[Q:  When should I use a wildcard App ID vs. an explicit App ID?](https://developer.apple.com/library/ios/qa/qa1713/_index.html) 


- [Provisioning profile](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppStoreDistributionTutorial/CreatingYourTeamProvisioningProfile/CreatingYourTeamProvisioningProfile.html#//apple_ref/doc/uid/TP40013839-CH33-SW1)：配置文件

开始 ios 开发时，最大的不适就是复杂的测试发布限制，你不能简单的编译个 app 放到任一设备中运行。比如符合 Provisioning profile 的设定。A picture worth a thousand words，先看下面的图：

![](/images/posts/itunes_connect/teamprovisioningprofile_2x.png)

一个 Provisioning profile 设置了 APP ID，证书和设备。当你使用某个 Provisioning profile 签名应用时，其实是使用它包含的证书来签名，并且签证对应的 APP ID，并只允许安装到被包含其中的设备中。


### Certification：证书
![](/images/posts/itunes_connect/certificates_2x.png)

如上图所示，证书的公钥保存在苹果的用户中心，公私钥对保存到开发者 keychain 中。你必须有私钥才可以签名应用。

- Development certification：开发证书
- Adhoc certification：真机部署测试证书
- Distribution certification：发布证书 

### Deployment Target：部署目标
面向的 ios 版本


### iTunes Connect
This is the version number shown in the App Store; This must be a pure version number like1.2.3


### Bundle Version (CFBundleVersion)
This doesn't need to be a pure version number. This can be something like 12345 or 1.2.3 (Build 12345AB). This is shown in the About window for Mac OS X apps for example and is often more a "Build Number" than a "Version Number". 真实版本号，用来判断版本信息的。


### Bundle Version String (CFBundleShortVersionString)
This value is used as the "real" version number. This must be the same string as used for the version in iTunes Connect. 面向用户市场的束的版本字符串--用户看到的版本号；
	

## 二 流程
### 下载安装证书

### 配置编译参数
- 设置 Deployment Target (部署目标)
	首选最新的 IOS 版本作为发布版本

### 填写 iTunes Connect 信息

打开[iTunes Connect](https://itunesconnect.apple.com)，填表：  

1. 第一组：
	- 选择语言
	- 应用名称
	- skunumber：一个用来识别app的特殊字符串。
	- bundleID

1. 第二组
	- 版本
	- 版权
	- 软件首类别
	- 次类别
	- 评级划分
	- 内容描述
	- 关键字
	- 技术支持url，以及下面的技术负责人的名字，姓，email，手机号码


填写完成后如下图所示：
![](/images/posts/itunes_connect/itunes_connect.png)

### 上传安装包

#### 使用 xcode 直接上传：
1. 检查代码，去除 log，debug 等相关的信息
1. 清理项目（clean project）
1. 生成包文件（archive）
1. 发送包文件（submit）
1. 选择发布证书（注意要使用 Agent 账号上传）
1. 发布成功

#### 使用 application loader 上传
以后补充

### itunes connect 关联上传的包文件
上传成功之后，在 Build 分类下，可以关联此包。

### 提交审核（submit for review）
以后补充