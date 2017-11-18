---
layout: post
title: "Grit - Chromium 国际化（2）"
category: "chromium"
tags: ["grit", 'chromium', 'i18n', 'l10n']
---


经过上一篇的分析，虽然对工作原理有了简单认识，但实际修改或添加支持多语言的文本还是蛮麻烦。所以这里提供一个小工具，简化这个流程。

下面说一下工具的使用：

###	1. 准备修改前的文件

- 修改前的默认语言文件（grd）
```
chromium_strings.grd
```
- 修改前的翻译文件（xtb）
```
chromium_strings_zh-CN.xtb
```

将2个文件放到工具的 `old` 目录中。 

> 注意如果有的 grd 文件中引用了其它资源，需要将这些资源一并放到 `old` 目录中

###	2. 准备新的默认语言文件（grd）
根据需要，修改旧的默认语言文件，最好将新加的文本放到文档的最后，以便和 chromium 原有的文本作区分。修改完成后的将文件放到 `new` 目录中。 这里，我将修改好的 `chromium_strings.grd` 放置到 `new` 目录。

> 注意如果有的 grd 文件中引用了其它资源，需要将这些资源一并放到 `old` 目录中



### 3. 执行

准备好的目录结构：

![](/images/posts/grit/grit-tool-structure.jpg)
 

运行根目录的 `run.bat`，它会根据上面的三个文件生成新的翻译文件 `xtb`。

*run.bat*

```bat
@echo off

echo handling chromium_strings.grd...
python3 "generate_translation_bundle.py" "--old_grd" "old/chromium_strings.grd" "--old_xtb" "old/chromium_strings_zh-CN.xtb" "--new_grd" "new/chromium_strings.grd" "-o" "new/chromium_strings_zh-CN.xtb"
IF ERRORLEVEL 0 ECHO Success!

echo handling generated_resources.grd...
python3 "generate_translation_bundle.py" "--old_grd" "old/generated_resources.grd" "--old_xtb" "old/generated_resources_zh-CN.xtb" "--new_grd" "new/generated_resources.grd" "-o" "new/generated_resources_zh-CN.xtb"
IF ERRORLEVEL 0 ECHO Success!

cmd /k

```

新生成的 xtb 实际是 xml 格式，将包含三种类型的结点（node）：
- 未修改：该类结点和原结点格式一样
- 修改：该类结点是修改了原文档内容，为了方便，生成文件会包含原翻译，用作参考。此类结点包含属性 `TODO="NEED_UPDATE"`
- 新添加：该类结点是新添加的文本，为了方便，生成文件会包含默认语言（grd）的文本。此类结点包含属性 `TODO="NEED_TRANSLATE"`

如：

```xml
<?xml version="1.0" ?>
<!DOCTYPE translationbundle>
<translationbundle>
  <translation id="6970811910055250180">正在更新设备...</translation>
  ...

  <translation TODO="NEED-UPDATE" id="4109624392265076313">Chromium</translation>
  ...

  <translation TODO="NEED-TRANSLATE" desc="Select the theme." id="233572109030077464">
    Select Theme
  </translation>
  ...

</translationbundle>
```

附： [Chromium-grit 工具](https://github.com/chengxuebin/chromium-grit)


### <span class="text-danger">提醒：</span>

* 保证 `old` 和 `new` 中的 grd 文件行数对应，因为是按照行号对应查找旧翻译文的，否则编译时出现错误
* 如果修改 zh-CN 以外的语言，需要手动修改生成的 xtb 文件中 `lang="zh-CN"` 属性