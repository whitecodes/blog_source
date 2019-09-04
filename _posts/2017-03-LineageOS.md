---
title: LineageOS
date: 2017-03-26 19:50:49
tags:
- Code
---
手机用的`Lineage OS`系统出问题了，最后刷机解决了。这里纪录下过程。
<!--more-->
# 奇怪的问题
周五的时候手机放在公司桌子上充电。充电线有点问题，敲键盘的时候重点就重一点，屏幕就开始闪，不停地在`省电模式`和正常之间切换。当时没有多想。之前也碰到过了。

之后用手机的时候才发现`home`键和`menu`键都失灵了。开发模式也进不去了。`Lineage OS`的各种功能都失效了，但是电话还能打通。暂时放下了，回去再想办法。

# 初步猜想
初步猜想是`SELinux`的问题。仔细查看后，连储存的权限也没有，连导出备份也不能。

+ `Recovery`中有`修复文件和权限问题`的选项，使用后无效
+ 回复出厂设置，无效
+ 双清，重新刷入当前系统，无效

最后，彻底刷清刷入`Recovery`和`Lineage OS`。

# 解决
`Recovery`模式下手机连接电脑。
电脑是`Linux`直接使用包管理器安装相关驱动，没`win`麻烦。
```bash
yaourt -S android-tool
# 查看手机是否已经正确连接
adb devices
adb reboot bootloader
```
这时手机重启进入`bootloader`模式。
```bash
fastboot devices
fastboot flash recovery twrp.img
```
之后控制手机重启进入`Recovery`模式。
连接电脑，把`Lineage OS`、`GAPPS nano`、`LineageOS su add-on`的刷机包传入手机中。

`Recovery`中刷入`Lineage OS`，重启进入系统。确认系统可以使用。如果储存还是不能使用，重启进入`Recovery`使用`Fix context`选项。

之后依次刷入`GAPPS nano`和`LineageOS su add-on`。