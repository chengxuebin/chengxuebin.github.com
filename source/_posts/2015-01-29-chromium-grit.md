---
layout: post
title: "Grit - Chromium 国际化（1）"
category: "chromium"
tags: ["grit", 'chromium', 'i18n', 'l10n']
---


## 整体流程
chrome_string 是关于国际化的项目（Project）， 这是一个只包含 `build step` 定义的空项目， `build step` 中定义了该项目编译时执行的程序或命令。在VS2010下编辑build step 可以用文本编辑软件直接打开 `chrome_strings.vcxproj`。

chrome_string 会在 chrome 编译之前编译，它将国际化定义的文件转换成资源文件（.pak）和 C++ 头文件，C++ 头文件将作为源代码在编译 chrome 时一起被编译。

 
语言包编译过程

![](/images/posts/grit/grit.jpg)

##	Grit 工具
grit（Google Resource and Internationalization Tool）是一个python开源工具，用于支持多国际化，位于 `src/tools/grit` 目录下。`chrome_string` 编译时会执行 `grit.py` 可以，然后由grit工具将grd和xtb文件编译成pak资源文件。

##	国际化文件分类
- `generated_resources.grd`：该文件定义了大部分国际化字符串定义，包括按钮的文字，窗口的标题等。
- `chromium_string.grd`：文件包括 generated_resources.grd 之外的国际化字符串定义，其中包含“安装、卸载”、“关于”等内容，把它独立出来主要是因为要和谷歌chrome 区别开来。
- `google_chrome_strings.grd`：用于编译 chrome 时使用，是和 chromium_string.grd 相对应的一个文件。
- `locale_settings.grd`：该文件定义了多语言的样式配置，例如窗口的大小，某些控件的尺寸，样式可以根据语言的不同而变化。

##	国际化文件的格式
###	grd 文件格式
```
<grit base_dir="." latest_public_release="0" current_release="1"
      source_lang_id="en" enc_check="m?l">
  <outputs>
    <output filename="grit/google_chrome_strings.h" type="rc_header">
      <emit emit_type='prepend'></emit>
    </output>
    <output filename="google_chrome_strings_zh-CN.pak" type="data_package" lang="zh-CN" />
    …
  </outputs>
  <translations>
    <file path="resources/google_chrome_strings_zh-CN.xtb" lang="zh-CN" />
    …
  </translations>
  <release seq="1" allow_pseudo="false">
    <messages fallback_to_english="true">
      <message name="IDS_PRODUCT_NAME" desc="The Chrome application name">
        Google Chrome
      </message>
      …
    </messages>
  </release>
</grit>
```

grd 是xml的格式，它有三个部分： `outputs`、`translations`、`release`。
- `outputs` 中定义编译出的C++头文件的位置和语言资源文件的位置，默认是在 `src/build/Debug` 或 `Release/obj/global_intermediate/chrome/`
- `translates` 中定义了多语言的定义文件的位置，每一种支持的语言都应该有一个翻译文件，这些文件将作为输入。
- `release` 中的 messages 定义的是默认语言的定义，当程序 找不到合适的语言显示时，将使用默认语言中的定义。
	message 的属性如下：
	- `name`：标识一个字符串的变量名
	- `desc`：该变量的介绍
	- `translateable`：是否使用翻译，默认true
	- `use_name_for_id`：是否使用name作为翻译的id标识。默认是false，翻译文件（xtb）中的id使用转换后的数字做映射；如果true，则使用 name 的值来和 grd 文件中的记录来映射。

###	xtb 文件格式
```
<!DOCTYPE translationbundle>
<translationbundle lang="zh-CN">
<translation id="6676384891291319759">访问互联网</translation>
…
</translationbundle>
```

xtb 是多语言的翻译文件，也是xml格式，每种语言对应一个xtb文件。`<translateion/>` 结点中的 id 的数字对应于 grd 文件中 `<message/>` 中的 `name` 或 `content` 值，注意默认不是根据 `name`，而是根据内容来生成 `id `值，这样的好处是可以去除冗余的文字。 转换的过程定义在 `/src/tools/grit/external/FP.py` 中。

## 国际化自定义步骤
1. 打开 grd 文件，添加 message 结点，或找到要修改的 message 结点；
1. 根据 message 的内容运行grit 工具，得到翻译文件 xtb 的id值；
1. 打开要修改的 xtb 文件，每种语言对应一个xtb 文件， 新建 translation 结点，id为第2步中计算得到的 id 值，结点内容为对应语言的翻译， 如果不知道如何计算 id，也可以 将 message 结点的属性use_name_for_id 设置成true，则 translate 的id 对应为 message 的name值；
1. 重新编译 chrome_string 项目，生成新的头文件和资源包。

改动文件：

```
chromium_strings.grd
generated_resource.grd 
chromium_strings_zh-CN.xtb 
generated_resources_zh-CN.xtb 
```