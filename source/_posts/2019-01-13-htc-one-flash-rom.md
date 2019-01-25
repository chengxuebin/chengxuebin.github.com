---
layout: post
title: "HTC ONE（M8）刷机"
category: "修理"
tags: ["android","修理"]
---

手中的一部 Verison HTC ONE 测试机是 4.0 系统，有些软件已经不能安装，抱着“老树开新花”的态度尝试搜了一下，果然已经有比较新的 6.0 系统了。首先用系统内的更新，由于手机是国外运营商版本，再加上更新源也在国外，系统内直接更新一直提示不成功。通过谷歌上查询，发现了一个 HTC 手机资源很全的网站，包含了 ROM 和 Firmware，以及刷机用的工具，比如第三方 Recovery。这里是网址：

[HTC 资源下载列表](https://forum.xda-developers.com/verizon-htc-one-m8/general/index-download-list-verizon-htc-one-m8-t3338285)

<!-- more -->

那就开始更新吧！

### 基础操作

* `电源` + `音量减`：手机进入 Bootloader
* `电源` + `音量加`：强制重启


### 1 刷 Firmware

#### 1.1 下载

下载 [m8vzw_AllInOne_Firmware_6.21.605.3.zip](https://qc3.androidfilehost.com/dl/p4aUy2hL8wtK0mkaFUxC2g/1547492410/24499762636007684/m8vzw_AllInOne_Firmware_6.21.605.3.zip)

#### 1.2 刷入手机

1. firmware 下载到 SD 卡
2. 将手机重启到 fastboot 模式。开机同时按下电源+音量减
3. 连接手机和 PC
4. 将手机进入 RUU 模式：`fastboot oem rebootRUU`
5. 开始刷入 zip：`fastboot flash zip firmware.zip`
6. 手机会显示更新未完成，需要再执行一次：`fastboot flash zip firmware.zip`
7. 重启进入bootloader：`fastboot reboot-bootloader `

### 2 刷第三方 Recovery

#### 2.1 下载镜像 IMG

下载 [twrp-3.2.3-1-m8.img](https://dl.twrp.me/m8/twrp-3.2.3-1-m8.img.html)

#### 2.2 下载 twrp apk

通过 twrpapp 可以非常便捷的实现 root 和刷入镜像：

[twrpapp-26.apk](https://dl.twrp.me/twrpapp/me.twrp.twrpapp-26.apk.html)

### 3 刷 ROM

#### 2.1 下载 6.0 ROM

参考这个[页面](https://forum.xda-developers.com/verizon-htc-one-m8/development/wip-4-17-605-9-stock-resources-firmware-t3196906)，直接下载 [6.21.605.3_M8_WL_M60_SENSE70_VZW_MR_AROMA-FINAL.zip](https://qc3.androidfilehost.com/dl/KyPIX5OAK-OoodvZb1iSPQ/1547492497/673368273298951216/6.21.605.3_M8_WL_M60_SENSE70_VZW_MR_AROMA-FINAL.zip)

#### 2.2 刷入 ROM

1. 将 rom 保存到 SD 卡
2. 将手机重启到 fastboot 模式。开机同时按下电源+音量减
3. 连接手机和 PC
4. 选择进入 Recovery 模式
5. 选择要刷入 zip
6. 根据自己的需求定制软件，完成 rom 安装



