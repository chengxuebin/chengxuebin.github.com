---
layout: post
title: "WebBrowser 设置代理"
category: "browser"
tags: ["Windows","WebBrowser","c++"]
---


## 设置方式一：`UrlMkSetSessionOption`

该方式只作用于本程序的 WebBrowser 组件，不修改系统代理设置，所以对其它应用程序没有影响。

```c++
std::string ws2s(const std::wstring& wstr){
  using convert_typeX = std::codecvt_utf8<wchar_t>;
  std::wstring_convert<convert_typeX, wchar_t> converterX;
  return converterX.to_bytes(wstr);
}

INTERNET_PROXY_INFO proxyInfo;
proxyInfo.dwAccessType = INTERNET_OPEN_TYPE_PROXY;
proxyInfo.lpszProxyBypass = NULL;
// UrlMkSetSessionOption only appears to work on either ASCII or
// multi-byte strings, not Unicode strings. Since the INTERNET_PROXY_INFO
// struct hard-codes to LPCTSTR, and that translates into LPCWSTR for the
// compiler settings we use, we must use the multi-byte version here.
// Note that for the count of input characters, we can use -1, since
// we've forced the string to be null-terminated.
proxyInfo.lpszProxy = reinterpret_cast<LPCTSTR>(ws2s(L"127.0.0.1:808").c_str());

UrlMkSetSessionOption(INTERNET_OPTION_PROXY, &proxyInfo, sizeof(INTERNET_PROXY_INFO), 0);
```

<!-- more -->


## 设置方式二：`InternetSetOption`

**自动代理PAC** 

```cpp
INTERNET_PER_CONN_OPTION_LIST    List;
INTERNET_PER_CONN_OPTION         Option[2];
unsigned long                    nSize =
sizeof(INTERNET_PER_CONN_OPTION_LIST);

Option[0].dwOption = INTERNET_PER_CONN_AUTOCONFIG_URL;
Option[0].Value.pszValue = "http://myserver/proxy.pacl";
Option[1].dwOption = INTERNET_PER_CONN_FLAGS;
Option[1].Value.dwValue = PROXY_TYPE_AUTO_PROXY_URL;

List.dwSize = sizeof(INTERNET_PER_CONN_OPTION_LIST);
List.pszConnection = NULL;
List.dwOptionCount = 2;
List.dwOptionError = 0;
List.pOptions = Option;

hMyHandle =  InternetOpen("", INTERNET_OPEN_TYPE_DIRECT, NULL, NULL, 0);

if(!InternetSetOption(hMyHandle, INTERNET_OPTION_PER_CONNECTION_OPTION,
&List, nSize))
   printf("InternetQueryOption failed! (%d)\n", GetLastError());
```

除了自动代理方式，还有其它方式，请看 *参考 1*

## 参考

1. [selenium proxy manager](https://github.com/SeleniumHQ/selenium/blob/master/cpp/iedriver/ProxyManager.cpp)
2. {% post_link mitm-proxy %}




