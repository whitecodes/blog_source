---
title: 在没有ROOT权限的限制下使用小米
tags:
  - Code
  - App
date: 2021-01-01 21:36:39
---

在等待解锁`Bootloader`的时候，尝试在没有获得`ROOT`权限的情况下使用手机。这里记录下尝试过程。
<!--more-->
## 想要使用的App

[App Ops](https://appops.rikka.app/)

[Fluid Navigation Gestures](https://play.google.com/store/apps/details?id=com.fb.fluid)

[Storage Isolation](https://sr.rikka.app/)

[WADB]( https://github.com/RikkaApps/WADB)

[小黑屋]( https://play.google.com/store/apps/details?id=web1n.stopapp)

## 尝试 Shizuku 绕过ROOT权限

通过`Shizuku`可以使其他应用调用高权限的 API。启动`Shizuku`看介绍可以通过`ADB`命令来实现，可以不依赖系统的`root`。

在`Shizuku`的[下载页面](https://shizuku.rikka.app/download.html)提供了4中下载方式：

- `Google Play`在没有安装代理的情况下受网络影响不行
- `GitHub Release`尝试了，同样因为网络的原因不行
- `Coolapk`页面原本是提供`apk`文件的下载的，现在需要通过应用扫码才能下载，不行
- 剩下的就是`IzzyOnDroid F-Droid Repository`，幸好可以直接下载。

接下来是运行`Shizuku`，提供了3种方式：

- `ROOT`首先被排除，有`ROOT`权限也就没有这篇`blog`了
- `Wireless debugging`因为`MIUI`没有提供这个选项，虽然是基于`Android 10`的
- 剩下的选择就是使用`ADB`了

1. 开启`开发者选项`：在`设置`->`我的设备`->`全部参数`页面上连续点击`MIUI 版本`就会进入`开发者模式`。
2. 开启`ADB`选项：在`设置`->`更多设置`->`开发者选项`->`调试`页面打开`USB 调试`和`USB 调试（安全设置）`
3. 数据线连接手机和PC，在弹出的`USB 的用途`弹框中，从`仅限充电`修改为`传输文件（MTP）`。否则可能会连接失败
4. 在PC上开终端输入`adb devices`检查有没有出现目标设备
5. 输入命令启动`Shizuku`

> adb shell sh /data/user_de/0/moe.shizuku.privileged.api/start.sh

一开始的时候连着线显示：`Shizuku`正在运行，拔掉线后就停止运行了。参考[用户指南](https://shizuku.rikka.app/zh-hans/guide/setup.html)最后的方法

> 你可以尝试打开网络 adb（使用指令`adb tcpip 5555`）后再启动 Shizuku。

使用这个方法后的确可以保持`Shizuku`运行，但是在运行`App Ops`请求相关权限的时候发现没用。然后才看到了在`用户指南`上的说明

> 对于 MIUI 11 及以上版本，你必须在 Shizuku 内授予使用者应用权限。这是因为自定义权限功能被破坏，参见 [Shizuku #45](https://github.com/RikkaApps/Shizuku/issues/45) 和 [android-in-china/Compatibility #16](https://github.com/android-in-china/Compatibility/issues/16)

到这里，我放弃了使用`Shizuku`


## 使用Google服务

`MIUI`在`设置`->`帐号与同步`->`谷歌基础服务`提供了一个开关。具体有什么作用不清楚，但是我还是先打开了。

在自带`应用商店`中可以搜索`Google`可以看到`Google Play 商店`。

代理用的是`v2rayNG`，网上找了几个都是在国内不能正常访问的，最后还是使用`奶牛快传`传了`apk`到手机中，手动安装。

这样基本就可以使用`Google`服务了。

## 使用管理员模式绕过root

在`App Ops`中看到还有一个`托管设备管理员模式`就尝试用这个。
参考[小黑屋免 Root 模式使用配置方法](https://github.com/web1n/Stopapp-Docs/blob/master/Device%20Owner%20%EF%BC%88%E5%85%8D%20root%EF%BC%89%E6%A8%A1%E5%BC%8F%E8%AE%BE%E7%BD%AE.md)来操作。

1. 开启`ADB`，手机上记得开启`USB 调试（安全设置）`
2. 注销所有登录的账户，包括小米账户
3. 如果您之前设置过访客模式、应用双开或是多用户设置等，也需要一并关闭或删除
4. 在电脑上执行命令：

> adb shell dpm set-device-owner web1n.stopapp/.receiver.AdminReceiver

现在`小黑屋`和`App Ops`都可以运行了。

`FNG`在隐藏导航键的时候需要高权限，需要用到`ADB`

> adb shell pm grant com.fb.fluid android.permission.WRITE_SECURE_SETTINGS

`Storage Isolation`和`WADB`都确切要求`ROOT`权限，无解。

在没有获取`ROOT`权限情况下，能做就到这里了。之后在解锁`Bootloader`前还有更多的使用，我也会更新在这里。
