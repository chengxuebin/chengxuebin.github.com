---
layout: post
title: "聊聊 WebRTC"
category: "webrtc"
tags: ["webrtc"]
---

Web Real-Time Communication（Web实时通信，WebRTC）由一组标准、协议和JavaScript API组成，用于实现浏览器之间（端到端）的音频、视频及数据共享。WebRTC使得实时通信变成一种标准功能，任何Web应用都无需借助第三方插件和专有软件，而是通过简单地JavaScript API即可完成。

## 名词

### 1 STUN

Session Traversal Utilities for NAT (STUN) – RFC 5389

### 2 TURN

**中继方式 NAT 穿透**（Traversal Using Relay NAT (TURN) – RFC 5766）

### 3 ICE 

**交互式连接建立**（Interactive Connectivity Establishment (ICE) – RFC 5245）

一种综合性的NAT穿越技术，它是一种框架，可以整合各种NAT穿越技术如STUN、TURN（Traversal Using Relay NAT 中继NAT实现的穿透）。ICE会先使用STUN，尝试建立一个基于UDP的连接，如果失败了，就会去TCP（先尝试HTTP，然后尝试HTTPS），如果依旧失败ICE就会使用一个中继的TURN服务器。

通信的两侧可能会处于不同的网络环境中，有时会存在好几层的访问控制、防火墙、路由跳转，所以我们需要一种方法在复杂的网络环境中找到对方，并且连接到相应的目标。WebRTC 使用集成了 STUN、TURN 的 ICE 来进行双方的数据通信。

<!-- more -->

### 4 SDP

**会话描述协议**（Session Description Protocol）

为了连接到其他用户，我们必须要对其他用户的设备情况有所了解，比如音频视频的编码解码器、使用何种编码格式、使用何种网络、设备的数据处理能力，所以我们需要一张“名片”来获得用户的所有信息，而 SDP 为我们提供了这些功能。

一个 SDP 的握手由一个 offer 和一个 answer 组成。

### 5 RTCPeerConnection 对象

RTCPeerConnection 对象是 WebRTC API 的入口，它负责创建、维护一个 WebRTC 连接，以及在这个连接中的数据传输。目前新版本的浏览器大都支持了这一对象，但是由于目前 API 还不稳定，所以需要加入各个浏览器内核的前缀，例如 Chrome 中我们使用 webkitRTCPeerConnection 来访问它。

### SCTP

### SRTP

### DTLS


## 架构

### WebRTC的分层协议图

![](https://image-static.segmentfault.com/266/264/2662644592-59ccfc5ce695f_articlex)

## 连接模型

### P2P Mesh

音视频数据流只在终端用户之间相互传输，不经过任何服务器节点，而且每个人都要与其它所有人建立P2P连接。

![](https://blog.wilddog.com/wp-content/uploads/2017/09/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7-2017-09-20-%E4%B8%8B%E5%8D%883.42.12.png)

![](https://pic4.zhimg.com/80/v2-1a208b10a56944a01331ee328415ce8b_hd.jpg)

### SFU 方案 


（Selective Forward Unit）。SFU从发布客户端复制音视频流的信息，然后分发到多个订阅客户端。典型的应用场景是1对多的直播服务。

![](https://blog.wilddog.com/wp-content/uploads/2017/09/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7-2017-09-20-%E4%B8%8B%E5%8D%883.46.26.png)

![](https://pic4.zhimg.com/80/v2-f46394c68633a826431d4424c27e9c5f_hd.jpg)


一个会议室 n 人，SFU 服务器的宽带是：

* incoming = bitrate * n 
* outgoing = bitrate * n + hangouts * n * (n-2)

说明：bitrate：主画面码率，hangouts：小窗口码率

participants | resolution（bitrate/hangouts）| incoming     | outgoing
:-----------:|:----------------------------:|:------------:|:--------:
4            | 720P（1.5Mbps/0.3Mbps）| 1.5x4=6Mbps  | 1.5x4+0.3x4x(4-2)=8.4Mbps
9            | VGA（0.6Mbps/0.3Mbps）| 0.6x9=5.4Mbps  | 0.6x9+0.3x9x(9-2)=24.3Mbps

### MCU

（Multi-point Control Unit）。MCU是传统视频会议系统中的核心控制单元，在WebRTC的系统实现中， 适合于多人音视频通话场景，MCU可以对接收到的多路流进行转码和混合，并向每个终端输出单路流。

![](https://blog.wilddog.com/wp-content/uploads/2017/09/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7-2017-09-20-%E4%B8%8B%E5%8D%883.45.14.png)

![](https://pic1.zhimg.com/v2-e91913906803cdd15faf31ce9ae2c614_r.jpg)

## NAT 穿透

### ICE


![](https://image-static.segmentfault.com/165/053/1650533365-59ccfcb53d363_articlex)


![](https://image-static.segmentfault.com/508/300/508300603-59ccfc7731a29_articlex)


![](https://image-static.segmentfault.com/146/004/1460044879-59ccfc85c5f6e_articlex)



### 单 PeerConnection 方案 VS 多 PeerConnection 方案

**单 PeerConnection 方案**

![](https://pic3.zhimg.com/80/v2-27399c759cda4accdd65a6caf79f7e4e_hd.jpg)

**多 PeerConnection 方案**

![](https://pic2.zhimg.com/80/v2-c14afdb905746f919c15cc85e4c12589_hd.jpg)


## 流程

### 标准WebRTC连接建立流程

![](https://pic3.zhimg.com/80/v2-f2e992dfb3c3252f90d7f8528ce22902_hd.jpg)

![](https://pic4.zhimg.com/80/v2-8cf643367610fad90cecc870b2795f4b_hd.jpg)

### 端口分配

![](https://pic3.zhimg.com/80/v2-1809b8c93983350d3e4484b35b6f6146_hd.jpg)

### Hangouts 方式

![](https://pic2.zhimg.com/80/v2-ef3a1ff96b99c10ef675e3f293af001d_hd.jpg)

### 视频最多人数

* 微信最多[9人](http://kf.qq.com/touch/faq/1211147RVfAV150401Ive2IN.html?platform=15)
* Google Hangouts - 一次会议中最多有25位参与者，过去是最多容纳10个人。当我第一次也是唯一一次使用它进行WebRTC培训时，参会者人数一超过10人就卡死了，导致了我只能选择使用其他视频会议服务。
* Hangouts Meet - 在单个会话中将其参与者人数限制在50人以内
* Houseparty - 8名参与者
* Skype - 25名参与者
* http://appear.in - 使用专业帐户登录，单个房间内最多支持12个参与者
* Amazon Chime - 桌面版16位参与者，iOS上最多8位参与者（尚未支持安卓）
* Atlassian Stride and Meet Jitsi - 50位参与者

## WebRTC API

WebRTC实现了三个API，分别是:

* MediaStream：通过MediaStream的API能够通过设备的摄像头及话筒获得视频、音频的同步流
* RTCPeerConnection：RTCPeerConnection是WebRTC用于构建点对点之间稳定、高效的流传输的组件
* RTCDataChannel：RTCDataChannel使得浏览器之间（点对点）建立一个高吞吐量、低延时的信道，用于传输任意数据

## 价格

### 即时通讯云

http://www.easemob.com/pricing/im


### 参考资料

1. [深入理解WebRTC](https://segmentfault.com/a/1190000011403597)
1. [聊聊WebRTC网关服务器1：如何选择服务端端口方案？](https://zhuanlan.zhihu.com/p/37400934)
1. [聊聊WebRTC网关服务器2：如何选择PeerConnection方案？](https://zhuanlan.zhihu.com/p/37493371)
1. [聊聊WebRTC网关服务器3：如何优化Server的线程方案？](https://zhuanlan.zhihu.com/p/37538078)
1. [聊聊WebRTC网关服务器4：QoS方案分析](https://zhuanlan.zhihu.com/p/37589412)
1. [WebRTC 是如何进行通信的，WebRCT 的三种网络结构](https://blog.wilddog.com/?p=2196)
2. [WebRTC网关服务器搭建：开源技术 vs 自行研发](https://zhuanlan.zhihu.com/p/40204873)
3. [即构自研WebRTC网关服务器架构实践](https://zhuanlan.zhihu.com/p/40429424)
4. [WebRTC视频通话中最多能容纳多少用户？](https://zhuanlan.zhihu.com/p/34558950)
5. [How Many Users Can Fit in a WebRTC Call?](https://bloggeek.me/how-many-users-webrtc-call/)
5. [WebRTC媒体服务器](https://zhuanlan.zhihu.com/p/33616446)
6. [WebRTC 开发实践：为什么你需要 SFU 服务器](https://zhuanlan.zhihu.com/p/56428846)

