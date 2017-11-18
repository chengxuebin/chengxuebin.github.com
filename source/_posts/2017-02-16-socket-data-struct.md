---
layout: post
title: "socket 常用数据结构"
category: "c++"
tags: ["c++","socket"]
---




### 编译步骤

#### 1. sockaddr 

```shell
struct sockaddr {
    unsigned short    sa_family;    // address family, AF_xxx
    char              sa_data[14];  // 14 bytes of protocol address
};
```

#### 2. inet 相关

sockaddr_in 继承 sockaddr 并添加了一些 INET 的数据属性。

```shell

// IPv4 AF_INET sockets:
struct in_addr {
    unsigned long s_addr;          // load with inet_pton()
};

struct sockaddr_in {
    short            sin_family;   // e.g. AF_INET, AF_INET6
    unsigned short   sin_port;     // e.g. htons(3490)
    struct in_addr   sin_addr;     // see struct in_addr, below
    char             sin_zero[8];  // zero this if you want to
};

// IPv6 AF_INET6 sockets:
struct in6_addr {
    unsigned char   s6_addr[16];   // load with inet_pton()
};

struct sockaddr_in6 {
    u_int16_t       sin6_family;   // address family, AF_INET6
    u_int16_t       sin6_port;     // port number, Network Byte Order
    u_int32_t       sin6_flowinfo; // IPv6 flow information
    struct in6_addr sin6_addr;     // IPv6 address
    u_int32_t       sin6_scope_id; // Scope ID
};
```


sockaddr 转换为 sockaddr_in：

```shell

sockaddr from;
sockaddr_in to = (sockaddr_in)from;

```


#### 3. sockaddr_storage

```shell
// General socket address holding structure, big enough to hold either
// struct sockaddr_in or struct sockaddr_in6 data:

struct sockaddr_storage {
    sa_family_t  ss_family;     // address family

    // all this is padding, implementation specific, ignore it:
    char      __ss_pad1[_SS_PAD1SIZE];
    int64_t   __ss_align;
    char      __ss_pad2[_SS_PAD2SIZE];
};
```


#### 4. addrinfo

```shell

struct addrinfo {
  int     ai_flags;          // AI_PASSIVE, AI_CANONNAME, ...
  int     ai_family;         // AF_xxx
  int     ai_socktype;       // SOCK_xxx
  int     ai_protocol;       // 0 (auto) or IPPROTO_TCP, IPPROTO_UDP 

  socklen_t  ai_addrlen;     // length of ai_addr
  char   *ai_canonname;      // canonical name for nodename
  struct sockaddr  *ai_addr; // binary address
  struct addrinfo  *ai_next; // next structure in linked list
};
```


[struct sockaddr and pals](http://beej.us/guide/bgnet/output/html/multipage/sockaddr_inman.html)
