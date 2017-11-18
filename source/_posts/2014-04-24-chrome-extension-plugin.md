---
layout: post
title: "Chrome扩展+NPPlugin"
category: "c/c++"
tags: ["chrome","c/c++","js"]
---


## 扩展“Chrome扩展”

虽然基于强大的Javascript和Chrome扩展接口 （Extension API）所实现的扩展简单且强大，但是，如果你已经有一些实现的很不错的本地程序（Native Program）要通过浏览器复用怎么办？或者，浏览器的API不满足需求，你需要使用某些更强大的本地接口（Native API）时呢？

你可以配合Chrome扩展，实现NPAPI插件，通过Javascript与本地的二进制程序交互。

其实Chrome本身已经集成了一些NPAPI插件，比如PDF viewer，Flash player。

先简单区分一下扩展（Extension）和插件（Plguin），它们的共同点是都用于扩充增强浏览器的功能，

- 扩展是基于 **浏览器提供的接口** ，运行在浏览器的沙箱（Sandbox）中，不与本地代码交互，安全性可控，也意味着可扩展的功能有限。

- 插件是基于与浏览器交互的框架（chrome用的是Mozilla的NPAPI框架），实现方式为本地二进制代码，插件其实就是能通过框架与浏览器通信的 **本地程序** 。它可以不运行在沙箱中，所以权限很大，实现不好很容易造成浏览器的漏洞。

&nbsp;
&nbsp;
&nbsp;

## 实现NPAPI插件


### 创建动态库（DLL）项目
1. 创建空项目  
本文通过VS2010创建，首选新建Win32|Win32 Project, 然后在创建向导中选择DLL, empty project。

2. 复制示例代码  
将示例包（文章附件）以下文件复制到项目中：

		common/*
		include/*
		lib/*
		Plugin.cpp
		Plugin.h
		PluginObject.cpp
		PluginObject.h

### 配置项目（Project）属性

1. 修改输出的动态库名  
修改Configuration Properties | General | Target Name，注意Mozilla规范要求，插件 **必须以“np”开头** 。

1. 添加Include目录  
在Configuration Properties | C/C++ | General | Additional Include Directories添加自定义目录include。

1. 添加第三方库  
这里要和前端JS交互，所以添加C++的第三方[JSON库](http://jsoncpp.sourceforge.net/)，
在Configuration Properties | Linker | General | Additional Library Directories添加链接库目录lib。  
然后在Configuration Properties | Linker | Input | Additional Dependencies里添加库文件lib_json.lib。

1. 添加宏定义（Preprocessor）  
在Configuration Properties | C/C++ | Preprocessor Definitions添加宏 **WIN32;_WINDOWS;XP_WIN32;XP_WIN;_X86_;**。

1. 禁止预编译头（Precompiled header)  
Configuration Properties | C/C++ | Precompiled Header为“Not Using Precompiled Headers”。

1. 添加.def文件   
在Configuration Properties | Linker | Input | Module Definition File中添加NPHello.def。

1. 修改Runtime Library  
这一步比较关键,在发布插件时去除debug符号，以“ **/MT** ”方式编译，不然在某些系统会遇到缺少运行时库的问题。
Configuration Properties | C/C++ | Code Generation | Runtime Library设置为/MT。

### 实现插件
1. 新建.def文件  
在项目中新建NPHello.def：  

	```cpp
	LIBRARY	"nphello"		
	EXPORTS
		NP_GetEntryPoints     @1
		NP_Initialize         @2
		NP_Shutdown           @3
	```

1. 修改mimetype  
npp_gate.cpp：
	
	```cpp
	char *NPP_GetMIMEDescription(void)
	{
		return "application/x-hello";
	}
	```

	NPHello.rc：
	
	```cpp
	BEGIN
        VALUE "CompanyName", "XB"
        VALUE "FileDescription", "XB Hello Module"
        VALUE "FileVersion", "1, 0, 0, 1"
        VALUE "InternalName", "nphello.dll"
        VALUE "LegalCopyright", "Copyright (C) 2013"
        VALUE "MIMEType", "application/x-hello"
        VALUE "OriginalFilename", "nphello.dll"
        VALUE "ProductName", "NPHello"
        VALUE "ProductVersion", "1, 0, 0, 1"
    END
	```

1. 删除NPP_GetJavaClass  
np_entry.cpp
	
	```cpp
	NPNFuncs.getJavaEnv              = NULL;
	NPNFuncs.getJavaPeer             = NULL;
	...
	pluginFuncs->javaClass  = NULL;
	```

1. 定义与Javascript交互的接口  
PluginObject.cpp

	```cpp
	// define function name called by JS
	const char* kPluginFunction = "function";
	// define property name set by JS
	const char* kPluginCallback = "callback";
	```

	接口1：用于JS查询Plugin对象是否有该方法：
	
	```cpp
	bool PluginObject::hasMethod(NPIdentifier methodName)
	{
		bool bRev = false;
		NPUTF8 *pName = NPNFuncs.utf8fromidentifier(methodName);
	
	  if (strcmp(pName, kPluginFunction) ==0){
	    return true;
	  } else {
		  return false;
	  }
	}
	```
	
	接口2：用于JS查询Plguin对象是否有该属性：
	
	```cpp
	bool PluginObject::hasProperty(NPIdentifier propertyName)
	{
		bool bRev = false;
		NPUTF8 *pName = NPNFuncs.utf8fromidentifier(propertyName);
	
		if (pName!=NULL){
	    if (strcmp(pName, kPluginCallback) == 0){
				return true;
	    }
		}
		return bRev;
	}
	```

1. Javascript调用Plugin的入口   
	
	```cpp
	bool PluginObject::invoke(NPIdentifier methodName,
	    const NPVariant* args, uint32_t argCount, NPVariant* result) { 
	
	  char* name = NPNFuncs.utf8fromidentifier(methodName);
	  bool ret_val = false;
	  std::string outString;
	
	  if (!name) {
	    return ret_val;
	  }
	  if (strcmp(name, kPluginFunction)==0){
	    ret_val = true;
	    hThread = CreateThread(NULL, 0, FunctionThread, this, 0, NULL);
	    outString = "Called plugin method from external.";
	  } else {
	    // Exception handling. 
	    outString = "Called an invalid method.";
	  }
	  char* npOutString = (char *)NPNFuncs.memalloc(outString.length() + 1);
	  if (!npOutString)
	    return false;
	  strcpy_s(npOutString, outString.length()+1, outString.c_str());
	  STRINGZ_TO_NPVARIANT(npOutString, *result);
	
	  NPNFuncs.memfree(name);
	  return ret_val;
	}
	```

1. 在新的线程中执行插件的逻辑

	```cpp
	// create a threed
	DWORD WINAPI FunctionThread(LPVOID lparam){ 
		PluginObject* pluginObj = (PluginObject*)lparam;
	  if (pluginObj==NULL)
	    return false;
	
	  pluginObj->PluginFunction();
	  return true;
	}

	void PluginObject::PluginFunction(){
	  try{
	    Sleep(3000);
	    Json::Value root;
	    root["msg"] = "Plugin process complete with three minutes!";
	    CallJSFunction(root.toStyledString());
	  } catch(HRESULT hr){
	    _com_error error(hr);
	    std::wstring msg(error.ErrorMessage());
	    Json::Value root;
	    root["msg"] = utf8_encode(msg);
	    CallJSFunction(root.toStyledString());
	  }
	}
	```

1. 当插件执行完毕，回调Javascript的方法

	```cpp
	void PluginObject::CallJSFunction(std::string result){
		int iRev = 0;
	
		if (mJSFunction != NULL){
			// 转换参数列表
			NPVariant relements[1];
	    	STRINGZ_TO_NPVARIANT(result.c_str(), relements[0]);
	
			// 调用JS函数
		    NPVariant jsResult; 
			NPN_InvokeDefault(npp, mJSFunction, relements, 1, &jsResult);
	
			if (NPVARIANT_IS_STRING(jsResult)){
				NPString rString = NPVARIANT_TO_STRING(jsResult);
	
				char revBuf[255] = {0};
				memcpy(revBuf, rString.UTF8Characters, rString.UTF8Length);
			}
	
			// 释放从浏览器那获取的结果
			NPN_ReleaseVariantValue(&jsResult);
		}
	  	return;
	}
	```

### 编译项目
以Release方式编译

&nbsp;
&nbsp;
&nbsp;


## 实现一个简单的扩展

### Manifest
注意plugins属性，如果插件只希望被本扩展调用，那将public设置成false。

```javascript
{
  ...
  
  "browser_action": {

    "default_popup": "popup.html",     
  },
  
  "plugins": [
    { "path": "plugins/nphello.dll", "public":false }
  ],
  
  "permissions": [
    "*://*/*"
  ]
}
```

### popup.html

```html
<!DOCTYPE html>
<html>
<head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
  <title>Hello</title>
</head>

<body> 
  <div></div>
</body>

<embed id="pluginObj" type="application/x-hello" style="width:0;height:0;"></embed>
<script src="js/jquery.min.js"></script>
<script src="js/popup.js"></script>
</html>
```

### popup.js
先设置回调函数，再去调用插件的方法。

```javascript
function callback(result){
  result = JSON.parse(result);
  print('<p class="npp"> [ NPP ]: '+ result['msg']+'</p>') 
  clearInterval(timer);
}

document.addEventListener('DOMContentLoaded', function () {
  pluginObj = document.getElementById("pluginObj");
  if (typeof pluginObj == "object") {
    pluginObj.callback = callback;
    print('<p class="js"> [ JS ]: I\'m calling Plugin function!</p>') 
    var msg = pluginObj.function();
    print('<p class="npp"> [ NPP ]: '+ msg+'</p>') 
  } 
  timer = setInterval(function(){count++; print('<p class="js"> [ JS ]: Hey, I\'ve been waiting '+500*count+' ms...')}, 500);
}, false);
```

### 运行结果  
![](/images/posts/extension_npp_result.jpg)

&nbsp;
&nbsp;
&nbsp;


附：[chrome extension+npplugin](https://github.com/chengxuebin/chrome_extension_npplugin)

扩展阅读：[使用 VS 编译 NPAPI 插件](https://developer.mozilla.org/en/docs/Compiling_The_npruntime_Sample_Plugin_in_Visual_Studio)
