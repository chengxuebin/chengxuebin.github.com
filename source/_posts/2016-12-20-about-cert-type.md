---
layout: post
title: "关于证书类型"
category: "crypto"
tags: ["crypto","cert"]
---



### 证书的编码（encoding）分类

首先，ASN.1 定义了几个编码格式，成为了 [X.690](https://en.wikipedia.org/wiki/X.690#DER_encoding) 标准，它们是：

* Basic Encoding Rules (BER)
* Canonical Encoding Rules (CER)
* Distinguished Encoding Rules (DER)

然后证书采用了 DER 作为 **二进制** 编码格式；但是为了便于阅读和传送，也可以将二进制转换为 base64 形式，又引入了 PEM 格式，如下所示：

* Privacy-enhanced Electronic Mail (PEM) ：将 DER 的做 base64 编码，并在开头和结尾加入标识：`"-----BEGIN CERTIFICATE-----" ` 和 `"-----END CERTIFICATE-----"`

### 证书的存储格式

* .pem
* .cer, .crt, .der
* .p7b, .p7c
* .p12
* .pfx
* .key

#### .pem

存储的是 base64 格式，也就是 PEM 格式

#### .cer, .crt, .der

通常存储的是 DER 编码的二进制格式

#### .p7b, .p7c

不包含数据的 PKCS#7 签名格式, 只包含证书或CRL
开头和结尾是：`"-----BEGIN PKCS7-----"` 和 `"-----END PKCS7-----"`

#### .pfx

personal information exchange (PFX) 也是 PKCS#12 格式，这是微软提出的格式

#### .p12

PKCS#12 格式，包含了证书和私钥，私钥有密码保护，Netscape 提出的格式

#### .key

用于 PKCS#8 的公钥或私钥，编码支持 DER 或 PEM 2种格式


### 查看证书

#### 查看 DER 证书

```shell
openssl x509 -in certificate.der -inform der -text -noout
```


#### 查看 PEM 证书

```shell
openssl x509 -in cert.pem -text -noout
openssl x509 -in cert.cer -text -noout
openssl x509 -in cert.crt -text -noout
```


### 编码转换

#### DER -> PEM

```shell
openssl x509 -in cert.crt -inform der -outform pem -out cert.pem
```

#### PEM -> DER

```shell
openssl x509 -in cert.crt -outform der -out cert.der
```


#### PEM -> P7B

```shell
openssl crl2pkcs7 -nocrl -certfile certificate.cer -out certificate.p7b -certfile CACert.cer
```


#### PEM -> PFX

```shell
openssl pkcs12 -export -out certificate.pfx -inkey privateKey.key -in certificate.crt -certfile CACert.crt
```

#### PFX -> PEM

```shell
openssl pkcs12 -in certificate.pfx -out certificate.cer -nodes
```


#### P7B -> PEM

```shell
openssl pkcs7 -print_certs -in certificate.p7b -out certificate.cer
```


#### P7B -> PFX

```shell
openssl pkcs7 -print_certs -in certificate.p7b -out certificate.cer

openssl pkcs12 -export -in certificate.cer -inkey privateKey.key -out certificate.pfx -certfile CACert.cer
```





