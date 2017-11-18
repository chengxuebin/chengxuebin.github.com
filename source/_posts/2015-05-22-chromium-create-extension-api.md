---
layout: post
title: "创建 Chromium Extension API"
category: "chromium"
tags: ["chromium", 'extension']
---




## 一、添加新接口描述文件

1. 新建描述文件

	创建 `chrome\common\extensions\api\wintrust.json`

	添加接口描述代码：
	
	```js
	[
	  {
	      // 获取浏览器模块目录
	      {
	        "name": "versionDirectory",
	        "type": "function",
	        "parameters": [{
	            "type": "function",
	            "name": "callback",
	            "parameters": [{
	              "type": "string",
	              "name": "path"
	            }]
	         }]
	      }
	    ]
	  }
	]
	```

1. 添加新文件到 `GYP`
 
	打开 `src\chrome\common\extensions\api\api.gyp`, 添加：
 
	```js
	...
	'schema_files': [
	  ...
	+  # 新接口	  
	+  'wintrust.json',
	],  
	```

1. 添加新文件到 `grd` 

	打开 `src\chrome\common\extensions_api_resources.grd`，添加
	 
	```xml
	<?xml version="1.0" encoding="UTF-8"?>
	<grit latest_public_release="0" current_release="1">
	  <outputs>
	    <output filename="grit/extensions_api_resources.h" type="rc_header">
	      <emit emit_type='prepend'></emit>
	    </output>
	    <output filename="extensions_api_resources.pak" type="data_package" />
	    <output filename="extensions_api_resources.rc" type="rc_all" />
	  </outputs>
	  <release seq="1">
	    <includes>
		  ...
	      <include name="IDR_EXTENSION_API_JSON_WINTRUST" file="extensions\api\wintrust.json" type="BINDATA" />
	    </includes>
	  </release>
	</grit>
	
	```

## 二、添加新接口头文件和实现

1. 新建头文件和实现文件

	创建 `src\chrome\browser\extensions\api\wintrust\wintrust_api.h` 和 `src\chrome\browser\extensions\api\wintrust\wintrust_api.cc`

1. 添加新文件到 `GYP`
 
	打开 `src\chrome\chrome_browser_extensions.gypi`, 添加：

	```js
	'sources': [
		...
	
    +   # win-trust 添加的插件接口
    +    'browser/extensions/api/wintrust/wintrust_api.h',
    +    'browser/extensions/api/wintrust/wintrust_api.cc',
	]
	```

1. 实现 `wintrust_api.h`
 

	```cpp
	#pragma once
	#include "chrome/browser/extensions/extension_function.h"
	
	namespace extensions {
	  class WintrustVersionDirectoryFunction : public SyncExtensionFunction {
	  public:
	    WintrustVersionDirectoryFunction();
	    virtual bool RunImpl() OVERRIDE;
	  protected:
	    virtual ~WintrustVersionDirectoryFunction(){};
	  private:
	    DECLARE_EXTENSION_FUNCTION("wintrust.versionDirectory", WINTRUST_VERSIONDIRECTORY);
	  };
	}
	```

1. 实现 `wintrust_api.cc`
 

	```cpp
	#include "chrome/browser/extensions/api/wintrust/wintrust_api.h"
	#include "base/file_util.h"
	#include "base/path_service.h"
	#include "chrome/browser/profiles/profile.h"
	
	namespace extensions{
	  
	  WintrustVersionDirectoryFunction::WintrustVersionDirectoryFunction(){}
	  
	  bool WintrustVersionDirectoryFunction::RunImpl() {
	    base::FilePath path;
	    PathService::Get(base::DIR_MODULE, &path);
	
	    scoped_ptr<base::StringValue> value (base::Value::CreateStringValue(path.value().c_str()));
	    
	    SetResult(value.release());
	    
	    return true;
	  }
	}
	```

## 三、重新生成项目（sln）

执行 `python build\gyp_chromium`


## 四、为新接口定义C++常量

- 打开 `src\chrome\browser\extensions\extension_function_histogram_value.h`

添加下面代码, 转换示例： `Example: "tabs.create" -> TABS_CREATE`
 
```cpp
enum HistogramValue {
  UNKNOWN = 0,
  ...
  
+  // 赢达信客户端添加的接口
+  WINTRUST_VERSIONDIRECTORY,

  ENUM_BOUNDARY // Last entry: Add new entries above.
};
```

## 五、注册新接口类（class）

- 打开 `src\chrome\browser\extensions\extension_function_registry.cc`

添加下面代码

```
// 赢达信客户端接口
#include "chrome/browser/extensions/api/wintrust/wintrust_api.h"

void ExtensionFunctionRegistry::ResetFunctions() {
#if defined(ENABLE_EXTENSIONS)

  // Register all functions here.

  ...

  // 赢达信客户端定义的接口
  RegisterFunction<extensions::WintrustVersionDirectoryFunction>();

  
  // Generated APIs
  extensions::api::GeneratedFunctionRegistry::RegisterAll(this);
  
#endif  // defined(ENABLE_EXTENSIONS)
}

```

## 六、定义新接口的访问权限

1. 定义该接口可用于的扩展程序类型：

	打开 `src\chrome\common\extensions\api\_permission_features.json`
	
	添加下面代码， `extension_types` 定义了哪些类型的扩展可以使用新接口。
	 
	```js
	// Copyright (c) 2012 The Chromium Authors. All rights reserved.
	// Use of this source code is governed by a BSD-style license that can be
	// found in the LICENSE file.
	
	{
	  ...
	+  // 赢达信的接口
	+   "wintrust": {
	+    "channel": "stable",
	+    "extension_types": ["extension", "packaged_app","platform_app","hosted_app"]
	+  }
	}
	
	```

1. 定义权限的键名，该常量也显示到 `manifest permission` 中
 
	打开 `src\chrome\common\extensions\permissions\api_permission.h`，添加
 
	```cpp
	enum ID {
		// Error codes.
		kInvalid = -2,
		kUnknown = -1,
		
		// Real permissions.
		...
	+	kWintrust,
		kEnumBoundary
	};
	
	```

1. 实现该键名
 
	打开 `src\chrome\common\extensions\permissions\chrome_api_permissions.cc`，添加
 
	```cpp
	std::vector<APIPermissionInfo*> ChromeAPIPermissions::GetAllPermissions()
	const {
		struct PermissionRegistration {
		APIPermission::ID id;
		const char* name;
		int flags;
		int l10n_message_id;
		PermissionMessage::ID message_id;
		APIPermissionInfo::APIPermissionConstructor constructor;
		} PermissionsToRegister[] = {
		
		// Register extension permissions.
		...
		{ APIPermission::kWintrust, "wintrust" },
	
	...
	```


## 七、测试新接口

1. 创建扩展  `manifest` 文件
	 
	```js
	{
	  "name": "WinTrust API",
	  "manifest_version": 2,
	  "version": "1.1",
	  "description": "Test new extenslion api",
	  
	  "browser_action": {
	    "default_icon": {                    
	      "19": "img/icon19.png",           
	      "38": "img/icon38.png"            
	    },
	    "default_title": "VPN", 
	    "default_popup": "popup.html"        
	  },
	  
	  "background": {
	    "page": "background.html"
	  },  
	  
	  "permissions": [
	    "*://*/*",
	    "wintrust"
	  ]
	}
	
	```

1. 创建扩展  `manifest` 文件
	 
	```js
	chrome.wintrust.versionDirectory(function(path){
		console.log('Test reust:', path);
	})
	
	```