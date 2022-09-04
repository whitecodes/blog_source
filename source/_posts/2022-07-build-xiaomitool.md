---
title: 编译小米工具
tags:
  - Code
  - App
date: 2022-07-21 23:56:45
---

之前的`K30s`主板坏了，然后直接买了`K40s`。买的时候有`TWRP`已经适配了，但是在`xda`上还没有看到子论坛。然后直接下单了，想着到解锁的时候差不多就有了，但是……直到现在还是没有。

虽然没有基于`ASOP`的rom，但是`MIUI.EU`还是有的，也可以root。

但是在解锁的时候，发现之前解锁用的工具`xiaomitool-v2`不能进行解锁，有报错，这里就记录下怎么自己编译这个工具。

_最后刷入了`GSI`，并且换了手机，现在没有使用这个手机_

<!--more-->

## 编译 xiaomitool-v2

`AUR`源中有`xiaomitool-v2`，之前可以正常安装使用的。但是现在直接使用，解锁的时候报错`[getServiceToken] Missing serviceToken cookie`。

[github](https://github.com/francescotescari/XiaoMiToolV2/issues/23) 上有人报告了,但是`AUR`中的版本中没有合入修改，所以需要手动下载源码运行。

上面的页面中有人已经提供了[修改方案](https://github.com/francescotescari/XiaoMiToolV2/issues/23#issuecomment-904082515)，照做就可以了。

这样编译是可以，但是登陆的时候会显示不出二维码或者验证码。修改下`Java`版本就可以了，参考[这个](https://github.com/francescotescari/XiaoMiToolV2/issues/56#issuecomment-1058175712)

到这里，`xiaomitool-v2`就可以运行了。接下来是解锁。

## 解锁

解锁就很简单了，手机开`ADB`，连电脑，运行`xiaomitool-v2`，解锁，然后按照要求来就可以了。

## 刷入系统

现在系统有2个选择：`MIUI.EU`和`GSI镜像`。要刷`GSI镜像`就需要在原版`MIUI`的基础上刷入。

`MIUI.EU`刷入需要先刷入[卡刷包](https://androidfilehost.com/?fid=14655340768118476852),直接运行对应的脚本就可以了。之后再参考教程刷新最新的卡刷包。

`GSI镜像`是通用的，没有针对设备做具体的优化，可以在[这里](https://github.com/phhusson/treble_experimentations/wiki/Generic-System-Image-(GSI)-list)找到一个列表。明确适配K40s的[这个](https://github.com/Umang96/munch_pe_custom_gsi_releases)。按照教程就可以刷进去了。

## 小米开源

小米终于开源K40s的[内核](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/munch-s-oss)了，估计之后就会有rom可以刷了
