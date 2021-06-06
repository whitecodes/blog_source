---
title: 使用小米
tags:
  - Code
  - App
date: 2021-01-01 21:36:49
---

买了新手机`Redmi K30 Pro Zoom Edition`，带的系统是`MIUI 12.0.9`，这里写下刷机使用的记录
<!--more-->
## 解锁Bootloader

要刷机、`ROOT`的前提是解锁`Bootloader`。小米的解锁的方式就比较花时间，但是总比没有提供解锁方法的好。

### 绑定小米账户

解锁需要在手机系统内绑定小米账户，绑定的时候还需要插入Sim卡。绑定页面的打开路径是`设置`->`更多设置`->`开发者选项`->`设备解锁状态`

### 下载解锁工具

小米官方只提供了在`windows`下使用的解锁工具，其他系统可以用第三方的解锁工具[XiaoMiTool V2](https://www.xiaomitool.com/V2/)

在`AUR`中也有这个工具[xiaomitool-v2](https://aur.archlinux.org/packages/xiaomitool-v2/)

### 尝试解锁

手机上开始`USB调试`，数据线连接。
命令行`ADB`检查连接了。
运行`xiamitool`。
嗯……需要登录，使用和之前绑定用的同一个账户。
工具带有引导的，一步步走下去，手机重启进入`fastboot`，然后工具就不动了，直到超时……

命令行手动用`sudo fastboot device`检查下设备连着的。然后仔细想想猜测工具也需要运行这个命令，那么工具也需要使用高权限运行。

用命令行添加`sudo`执行，结果是报错需要等待7天。

等呗……

在等的过程中尝试在不`ROOT`的情况下使用手机

继续等呗……

## 7天后刷入 recovery

7天后终于可以解锁`Bootloader`了。

提前手动下载了对应的`twrp`版本，这里用的是`recovery-TWRP-3.4.1B-0328-REDMI_K30PRO-CN-wzsx150.img`。

运行`sudo xiamitool`，登录用户，然后解锁。

之后刷入`Recovery`。
刷入的时候会搜索网络，这个时候断开网络就会出现本地选择的选项，选择提前下载好的`twrp.img`就可以了

这里提供手动刷入的方法


解锁还是要使用工具


1. 手动进入`fastboot`

手机关机后，同时按住开机键和音量下，震动后松开开机键，出现 __蓝色__ 的`fastboot`字样和米兔图案。

2. 刷入`recovery`

```bash
fastboot devices
fastboot flash recovery twrp.img
fastboot boot twrp.img
```

3. 清除数据


不清楚这步是不是必需的


跳过输入密码，确认允许修改system分区
清除->格式化DATA分区，这步会清除之前系统中保存的所有数据
清除->高级清除选项->勾选Dalvik/ART Cache+Cache->确认清除
重启->Recovery

正常的情况下会重启进入`recovery`

## 刷入系统

接下来就是选择系统了

刷入国际版`MIUI`：主要尝试了[xiaomi.eu](https://xiaomi.eu/community/forums/miui-12.206/)，丢失了一些国内的特性。比较重要的是没了`AI电话助手`。

刷入基于`AOSP`或者基于`LineageOS`的其他`ROM`：基本会丢失所有`MIUI`的特性，包括摄像头。主摄基本都可用，但是其他摄像头成像都不理想。

我选择的是[crDroid](https://forum.xda-developers.com/t/rom-11-0-official-lmi-crdroid-v7-2-11-01-2021.4201615/)。主要是讨厌`MIUI`的大圆角。另外在试用`xiaomi.eu`中发现`小米相册`有大量的wifi上传流量。

按照帖子中的流程下载`miui_LMIGlobal_V12.2.4.0.RJKMIXM_682e9ad14f_11.0`、`crDroid Rom`、`openGapps`、`Magisk`。

刷入的流程按照帖子中的就可以了。

`crDroid`中大大小小的`bug`其实不少。其中比较大的就是`支付宝`不能打开网页，这个是可以[修复](https://forum.xda-developers.com/t/rom-11-0-official-lmi-crdroid-v7-2-11-01-2021.4201615/page-3#post-84111771)的:

>for those having problems on viewing content of Chinese apps from Alibaba(like Alipay, Taobao, DingTalk), just remove in root/product/build.prop: debug.hwui.renderer=skiavk or change it to debug.hwui.renderer=skiagl

另外还有`bug`是在锁屏界面:屏下指纹解锁和双击亮屏有冲突;面部识别解锁失败后摄像头会反复升起

## 摄像头

使用第三方`rom`比较伤的是摄像头功能只有主摄可用。

系统自带的不行，那么就找找第三方的。在[Google Camera Mod (Gcam)](https://forum.xda-developers.com/t/google-camera-mod-gcam.4113795/)找到了一些。

目前同时装的是[Urnyx05](https://www.celsoazevedo.com/files/android/google-camera/dev-urnyx05/)和[Arnova8G2](https://www.celsoazevedo.com/files/android/google-camera/dev-arnova8G2-beta/)，其中`Urnyx05`有`Zoom Edition`的配置文件，现在当作主力在用。

## 修改系统圆角

讨厌`MIUI`的大圆角，`Android 11`的圆角也有点不能忍。
`Masgisk`有模块[G-Visual Mod](https://github.com/Magisk-Modules-Repo/G-VisualMod)可以修改圆角半径。
