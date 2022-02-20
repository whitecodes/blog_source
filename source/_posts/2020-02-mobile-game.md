---
title: 在电脑上玩手机游戏
tags:
  - Code
  - Arknights
date: 2020-02-29 20:56:28
---

折腾了下在电脑上玩手机游戏
<!--more-->

# Windows 环境
在`windows`上运行`明日方舟`比较简单，官方就有提供PC运行的下载。

下载回来本质上是`网易`的`MuMu虚拟机`，搭配`明日方舟`的客户端。基本上支持CPU虚拟化的机器上的可以跑起来。

# Linux 环境
最开始是在`xps`上实现了远程游戏。使用的软件是[scrcpy](https://github.com/Genymobile/scrcpy)。名字和`Python`的那个爬虫框架相同，但是这个是通过`adb`来远程操控手机的。本质上还是在手机上跑游戏，电脑上同步手机屏幕和操作。

手机需要开启`adb`功能，为了方便最好还开启`adb over wireless`功能。

然后安装`scrcpy`。看github上的介绍提供了各个平台的下载，不止局限于`Linux`。

- 保持手机和电脑在同一个wifi下，手机建议全程连着电源
- 开启手机的`adb`和`adb over wireless`
- 查看手机在wifi下的`ip`，这个可以通过手机的`关于本机`查看，也可以通过路由器查看
- 运行下面的命令连接手机
``` shell
adb connect DEVICE_IP:5555
scrcpy -S
```

一切顺利的话：手机屏幕关闭，电脑上看到了出现手机屏幕的画面。接下来就是用鼠标代替触控来玩游戏了。如果黑屏了按下`Ctrl`+`p`，相当于按下电源键。

这个方案的好处是比较方便，而且兼容性比较高，支持更多的功能。更多的功能和选项请参看上面的链接。

~~然后`Sony`的`xz1c`阉割了`adb over wireless`功能。这就需要手机连着电脑才有效，放弃了这个方案。~~

`adb over wireless`功能在厂商的正式系统中有不提供的，例如`Sony`，需要用到`xda`的一个软件[ADB Tools](https://forum.xda-developers.com/android/apps-games/app-adb-tools-t2954734)来开启,这个软件需要`root`权限。或者用数据线开启`adb`后再用命令开启`adb over wireless`


# Anbox 方案
之后在网上发现了`Anbox`这个软件。本质上是运行一个后台服务提供硬件信息，这个就隔离了CPU的虚拟化。运行的是一个`android`的镜像。你需要手动安装游戏，感觉还是比较麻烦。仅仅验证了是可行的，但是没有实际操作

~~现在还是在手机上玩`明日方舟`。~~

__现在用`scrcpy`的方案在电脑上玩游戏。__
