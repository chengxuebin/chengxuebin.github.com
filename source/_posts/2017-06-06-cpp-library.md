---
layout: post
title: "C++ 常用方法收集"
category: "c++"
tags: ['c++']
---



## 字符操作

### wstring 和 string 互转

```cpp
std::wstring s2ws(const std::string& str) {
    using convert_typeX = std::codecvt_utf8<wchar_t>;
    std::wstring_convert<convert_typeX, wchar_t> converterX;

    return converterX.from_bytes(str);
}


std::string ws2s(const std::wstring& wstr){
    using convert_typeX = std::codecvt_utf8<wchar_t>;
    std::wstring_convert<convert_typeX, wchar_t> converterX;

    return converterX.to_bytes(wstr);
}
```

[进一步阅读](https://stackoverflow.com/questions/4804298/how-to-convert-wstring-into-string)

<!-- more -->

## Windows 相关

### 获取当前进程目录

```cpp
std::wstring getExePath() {
    wchar_t buffer[MAX_PATH];
    GetModuleFileName(NULL, buffer, MAX_PATH);
    std::string::size_type pos = std::wstring(buffer).find_last_of(L"\\/");
    return std::wstring(buffer).substr(0, pos);
}
```

