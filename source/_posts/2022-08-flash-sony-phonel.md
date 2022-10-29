---
title: Sony 手机使用
tags:
  - Code
  - App
  - Phone
date: 2022-08-08 23:38:18
---

之前买了红米K40s，解锁后没有合适的ROM,`MIUI`系列的永不习惯，`Android 12 GSI`因为圆角不喜欢。所以后来就买了二手的`Sony Xperia 1 II`。本来想买`Sony Xperia 5 II`的，但是看到有价格不错的`1 II`就直接买了。用下来还行。

拿到手是`Android 12`，刷回了`Android 11`，解锁`root`，停用了自带的一堆应用，用起来还行。

<!--more-->
## 解锁

`Sony`的手机解锁很简单，去[Sony 解锁页面](https://developer.sony.com/develop/open-devices/get-started/unlock-bootloader/)按照步骤提交手机的`IMEI`就可以了。

## 刷官方系统

下载官方系统用的是[XperiFirm](https://xperifirm.com)，刷机工具用的是[Newflasher](https://forum.xda-developers.com/t/tool-newflasher-xperia-command-line-flasher.3619426/)，解压工具[UnSIN](https://forum.xda-developers.com/t/tool-unsin-sin-v3-v4-v5-unpacker-v1-13.3128106/)。

1. 用`XperiFirm`下载对应的原厂系统。如果网络不好可以使用下载工具下载各个分包然后合并
2. 用`unsin`解压`boot_XXXXX.sin`得到`img`文件
3. 用`Magisk`给上面的`img`打上`root`，得到`rooted.img`
4. 用`Newflasher`将官方系统刷入
5. 手动使用`adb`刷入上面的`rooted.img`，分别执行下面的命令

```bash
adb reboot bootloader
fastboot flash boot rooted.img
fastboot reboot
```

## 记录下用的Magisk模块

- [G-Visual Mod](https://forum.xda-developers.com/t/module-g-visual-mod-systemlessy-customize-your-androids-visual.4225571/)：用来消除圆角的
- [MagiskHide Props Config](https://forum.xda-developers.com/t/module-magiskhide-props-config-safetynet-prop-edits-and-more-v6-1-2.3789228/)：用来隐藏底栏导航按键的，搭配`Fluid Navigation Gestures`使用的
