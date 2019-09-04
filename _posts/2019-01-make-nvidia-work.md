---
title: Arch Linux 中启用 XPS 的独显
date: 2019-01-13 20:54:36
tags:
- Code
---
前面一直用得都是`Intel`核显，花了一个晚上启用了独显。

这篇只写了启用`Nvidia`独显的方法，安装`Arch Linux`请参考之前的[在 XPS 15 9560 上安装 Arch Linux](https://whitecodes.github.io/2018/07/install-arch-on-XPS/)。

方法基本上是参考了[Dell XPS 15 9560](https://wiki.archlinux.org/index.php/Dell_XPS_15_9560#Graphics)
<!--more-->
# 现状
现在用的`Intel`的核显，屏蔽了`Nvidia`独显。

基本现状是播放1080p视频有些吃力，偶尔会出现掉帧。在线视频基本还是看720p，再高就会卡顿。

# 切换
最好的状况就是可以自动切换核显和独显。按照教程安装`Bumblebee`，然后配置，结果就是失败，并没有办法启用独显。

# 独显
核显用了一段时间，感觉独显不用浪费，于是还是想着要启用独显。尝试参考中的最后一种方法。
安装`Nvidia`驱动，`xorg-xrandr`已经装了就不需要安装了。

然后配置。
``` sh
cd /etc/X11/xorg.conf.d
ls
sudo nano xorg.conf
```

在文件的末尾添加上

``` conf
Section "Module"
    Load "modesetting"
EndSection

Section "Device"
    Identifier "nvidia"
    Driver "nvidia"
    BusID "1:0:0"
    Option "AllowEmptyInitialConfiguration"
EndSection
```

根据`Display Managers`的不同还要创建不同的配置文件。这里以`KDE`默认的`SDDM`为例。

``` sh
cd /usr/share/sddm
ls
sudo nano Xsetup
```

中间的命令用来确认文件是不是存在。没有修改过的话里面应该只有注释，添加上

``` conf
xrandr setprovideroutputsource modesetting NVIDIA-0
xrandr auto
```

然后重启。重启之后应该已经启动独显。要验证的话可以打开`信息中心`，在`图形信息`下的`OpenGL`中应该可以看到独显的信息。
也可以安装教程的方法，安装 `mesa-demos`来查看。

独显启动了，但是感觉窗口部件的显示怪怪的，基本可以认为是缩放的问题，尝试将缩放由原本的 250% 调整到 200% 。重启后，显示正常。

# 遗留问题
第二天开机发现看视频的时候有点画面撕裂，将缩放调整到 300% 重启后，问题消失。