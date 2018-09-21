---
layout: post
title: "中间人劫持代理"
category: "OpenSSL"
tags: ['openssl', 'proxy']
---



## 中间人劫持代理

![](/images/posts/mitm_proxy/how-mitmproxy-works-explicit-https.png)

注：图片来源于 mitmproxy

#### 本代理基于 TCP，是一个不关心协议的代理？
是的

#### HTTP 代理 VS Socks 代理



## 问题与挑战

### 获取远端主机名（Host）

当应用设置了代理后，在 TLS 协商前，应用首先会发一条 CONNECT 请求，期待通过代理和远端服务器建立 TLS 隧道（tunnel）。

```
CONNECT 10.1.1.1:443 HTTP/1.1
```

作为中间人代理，并不帮助建立隧道，而是需要解析 CONNECT 请求，并从中获得远端服务器主机名。同时返回给被代理程序隧道建立成功的回复：

```
// 根据请求指定版本号
HTTP/1.0 200 Connection established\r\n\r\n
```

<!-- more -->

### Subject Alternative Name

这是服务器证书包含的一个字段，用于标识证书签发的域名。

通常服务器的域名保存在 `Common name` 字段，只保存一个域名，而 `Subject Alternative Name` 可保存多个域名，并支持通配符。也就是，有的时候访问的服务器主机并不和 `Common name` 匹配，但匹配 `SAN`，这样的证书也是合法的。

### 服务器名称指示（SNI）

这是识别同一 IP 部署的多个带证书虚拟主机（Virtual Host）的技术。

基于名称的虚拟主机允许多个域名由同一IP地址上的单个服务器（通常为Web服务器）托管。使用 HTTP 时，其必须包含的协议头 [`Host`](https://zh.wikipedia.org/wiki/HTTP%E5%A4%B4%E5%AD%97%E6%AE%B5%E5%88%97%E8%A1%A8) 字段标识了欲访问的域名，据此分辨出虚拟主机。但是，由于 HTTPS 握手发生在服务器看到任何 HTTP 头之前，这时并不知道要访问哪个虚拟主机，也就不能把对应的服务器证书发给客户端，以完成 TLS 协商。

SNI 通过让客户端发送虚拟域名的名称作为TLS协商的一部分来解决此问题。

OpenSSL 客户端实现：

```cpp
// set host name before initiating the SSL connection
SSL_set_tlsext_host_name(ssl, hostName)
```

[扩展阅读](https://zh.wikipedia.org/wiki/%E6%9C%8D%E5%8A%A1%E5%99%A8%E5%90%8D%E7%A7%B0%E6%8C%87%E7%A4%BA)

### Certificate Pinning

简单地说，就是某些程序（比如浏览器）只相信某些根证书签发的服务器证书。

通常证书通过证书链验证合法性，`MyCert` 由 `IntermediateCert` 签发，它又由 `RootCert` 签发，`RootCert` 已经内置在操作系统的可信任根证书中。 这样，`MyCert` 是合法的。常见内置的根证书有：Verisign, Digicert, Thawte等。

但是，Certificate pinning 的作用是，即使通过了上面的验证，如果不是某个指定 RootCert 签发的证书，也认为其不合法。这在一定程序上避免中间人攻击，但没有真的解决。

下面是一些使用 Certificate pinning 的应用：

* 微软的自动更新
* 苹果 App Store
* Twitter app

[扩展阅读](https://security.stackexchange.com/questions/29988/what-is-certificate-pinning/)

## 设置代理

### 通过 `netsh winhttp` 命令

<a href="https://technet.microsoft.com/pt-br/library/cc731131(v=ws.10).aspx#BKMK_5">文档</a>

设置代理：

```shell
set proxy 127.0.0.1:8080 "<local>"
```

### 通过 `WinHTTP` 接口设置

<a href="https://msdn.microsoft.com/en-us/library/windows/desktop/aa384069(v=vs.85).aspx">文档</a>

### WinHTTP vs. WinINet

<a href="https://msdn.microsoft.com/en-us/library/windows/desktop/hh227297(v=vs.85).aspx">文档</a>

选择 WinINet 的原因：

* 支持 cookies
* Credential Cache (和SSL证书相关？)
* Credential Prompting (和SSL证书相关？)

### INTERNET_PER_CONN_OPTION

<a href="https://msdn.microsoft.com/en-us/library/windows/desktop/aa385145(v=vs.85).aspx">INTERNET_PER_CONN_OPTION</a>

### INTERNET_PROXY_INFO

```cpp
INTERNET_PROXY_INFO proxy;
proxy.dwAccessType = INTERNET_OPEN_TYPE_PROXY;
// 根据协议来设置代理，比如 FTP, HTTPS 等，使用下面的字符串
// ftp=http://<ProxyServerName>:80; https=https://<ProxyServerName>:80)
proxy.lpszProxy = (LPCTSTR)"127.0.0.1:8080";  // unicode building, must cast to LPCTSTR
proxy.lpszProxyBypass = NULL;

```


### 设置自动代理

```cpp
wchar_t wzsPath[MAX_PATH];
wchar_t wszProxyUrl[INTERNET_MAX_URL_LENGTH];
SHGetFolderPath ( NULL, CSIDL_APPDATA | CSIDL_FLAG_CREATE, NULL, 0, wszPath );

// Construct Pac file path. – %appdata%\MyAppFolder\redirect.pac
StringCbPrintf (wszProxyUrl, sizeof(wszProxyUrl), 
        L"file://%s\\MyAppFolder\\redirect.pac", wszPath );

INTERNET_PER_CONN_OPTION_LIST *optionList = 0;
optionList->pOptions[INDEX].Value.pszValue = wszPath;

InternetSetOption ( NULL, INTERNET_OPTION_PER_CONNECTION_OPTION,
        optionList, sizeof(INTERNET_PER_CONN_OPTION_LIST) );
```


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


### 参考

* [mitmproxy](http://docs.mitmproxy.org/en/stable/howmitmproxy.html)
* [如何给CHtmlView添加代理——MFC](http://www.5icpp.com/?p=139)
* [为网络程序添加代理设置](http://www.lai18.com/content/2206252.html)：带 VC6 示例程序
* [查询代理和设置代理](http://www.cnblogs.com/duanshuiliu/archive/2013/03/18/2965405.html)
* [写C++程序绕过IE的限制：“某些设置由系统管理员进行管理”](http://blog.csdn.net/ba_jie/article/details/43564385)

