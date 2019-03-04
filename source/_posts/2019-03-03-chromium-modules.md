---
layout: post
title: "Chromium 模块"
category: "chromium"
tags: ["chromium"]
---


| 模块       | 说明          | 必要性 |
|:-----------|:--------------|:---------:|
| default_apps| 默认安装应用的目录 | × |
| default_apps/external_extensions.json| 默认安装的配置文件 | × |
| Extensions | 默认安装扩展的目录 | × |
| Extensions/external_extensions.json | 默认安装的扩展的配置文件 | × |
| Installer/chrmstp.exe| chrome setup，和 setup.exe 完全一样 <sup>[1]</sup> | × |
| Installer/setup.exe| 安装文件，用于升级或卸载 | × |
| Locales| 多语言文件 | √ |
| VisualElements| ? | × |
| WidevineCdm| 自带组件（component），Widevine Content Decryption Module，用于在HTML5视频网站播放DRM加密的视频，如netflix | ×? |
| 64.0.3282.186.manifest| chrome 主清单文件 | √ |
| chrome.dll| chrome 主模块 | √ |
| chrome.dll.sig| 签名文件? | × |
| chrome.exe.sig| 签名文件? | × |
| chrome_100_percent.pak| 1x 资源包 | √ |
| chrome_200_percent.pak| 2x 资源包 | √ |
| chrome_child.dll| chrome 子进程模块？ | √ |
| chrome_child.dll.sig| 签名文件? | × |
| chrome_elf.dll| Chrome Early Loading Framework <sup>[2]</sup> | √ |
| chrome_watcher.dll| Watcher进程加载 chrome_watcher.dll | √ |
| d3dcompiler_43.dll| required for Windows XP。 Direct3D HLSL Compiler for Redistribution | × |
| d3dcompiler_47.dll| required for Windows Vista and newer。 Direct3D HLSL Compiler for Redistribution | × |
| eventlog_provider.dll| windows 的事件日志 | √? |
| ffmpeg.dll | 多媒体支持。如果不需要播放视频等 | × |
| icudtl.dat| International Components for Unicode Data File Little Endian，ICU库是一个支持国际化，本地化的软件库<sup>[3]</sup> | √  |
| libegl.dll| [Embedded-System Graphics Library](https://en.wikipedia.org/wiki/EGL_(API)) <sup>[4]</sup> | × |
| libglesv2.dll| [OpenGL for Embedded Systems (OpenGL ES or GLES)](https://en.wikipedia.org/wiki/OpenGL_ES) | × |
| nacl_irt_x86_64.nexe| Native Client integrated-runtime | √  |
| natives_blob.bin| binary large object，V8 引擎需要的数据文件 | √  |
| resources.pak| 资源文件 |  √  |
| snapshot_blob.bin| binary large object，V8 引擎需要的数据文件 | √  |
| v8_context_snapshot.bin| Create V8 contexts from a snapshot file. It will make the initializations of V8 contexts for 1.6-3 times faster. | √  |
| wow_helper.exe | win32与win64兼容工具 | √  |


<!-- more -->

### 浏览器支持

Support for WebGL is present in： 

* Firefox 4+
* Google Chrome 9+
* Opera 12+, Safari 5.1+
* Internet Explorer 11+
* Microsoft Edge build 10240+


### References


1. The Active Setup executable will be an identical copy of setup.exe; this is necessary because Windows' installer detection heuristics (which include things like process name being "setup.exe") will otherwise force elevation for non-admin users when setup.exe is launched. This is mitigated by adding requestedExecutionLevel="asInvoker" to setup.exe's manifest on Vista+, but there is no such manifest entry on Windows XP (which results in crbug.com/166473). TODO(gab): Rename setup.exe itself altogether and use the same binary for Active Setup.
1. [Chrome Early Loading Framework](http://gclxry.com/chrome-early-loading-framework/)
2. [International Components for Unicode](http://apps.icu-project.org/datacustom/)
2. [ANGLE(Almost Native Graphics Layer Engine)](https://en.wikipedia.org/wiki/ANGLE_(software))
/en.wikipedia.org/wiki/ANGLE_(software))
2. [CEF redistrib](https://bitbucket.org/chromiumembedded/cef/src/816f700d3ea42bedc5ca5a2314c27b761b69abc5/tools/distrib/win/README.redistrib.txt?at=master&fileviewer=file-view-default)
3. [The WebGL API](https://developer.mozilla.org/en-US/docs/Web/API/WebGL_API)
