---
layout: post
title: "Homebrew 用法"
category: "mac"
tags: ["homebrew", "mac"]
---

### 列出已安装软件

```shell
% brew list 

# 输出结果
autoconf        flyway          libgpg-error    libyaml         mysql           php71           pkg-config      ruby-build
automake        freetype        libksba         makedepend      nginx           php71-mcrypt    proxychains-ng  sphinx-doc
boost           gettext         libmemcached    maven           node            php71-xdebug    putty           telnet
cmake           icu4c           libpng          mcrypt          openssl         php@7.1         readline        unixodbc
composer        jpeg            libtool         memcached       openssl@1.1     phpmyadmin      rename          xz
coreutils       libevent        libxml2         mhash           pcre            phpunit         ruby            zlib
```

<!-- more -->

### 查看已安装软件信息

```shell
% brew info node                                                               

# 输出结果：
node: stable 9.11.1 (bottled), HEAD
Platform built on V8 to build network applications
https://nodejs.org/
/usr/local/Cellar/node/9.2.1 (5,125 files, 49.5MB)
  Poured from bottle on 2017-12-12 at 16:54:22
/usr/local/Cellar/node/9.4.0 (5,125 files, 49.7MB) *
  Poured from bottle on 2018-01-29 at 17:08:45
From: https://github.com/Homebrew/homebrew-core/blob/master/Formula/node.rb
==> Dependencies
Build: python@2 ✘, pkg-config ✔
Recommended: icu4c ✘
Optional: openssl ✘
==> Options
--with-debug
        Build with debugger hooks
--with-openssl
        Build against Homebrews OpenSSL instead of the bundled OpenSSL
--without-completion
        npm bash completion will not be installed
--without-icu4c
        Build with small-icu (English only) instead of system-icu (all locales)
--without-npm
        npm will not be installed
--HEAD
        Install HEAD version
==> Caveats
Bash completion has been installed to:
  /usr/local/etc/bash_completion.d
```

### 安装、卸载、升级

```shell
% brew install node

% brew uninstall node

% brew upgrade node
```






