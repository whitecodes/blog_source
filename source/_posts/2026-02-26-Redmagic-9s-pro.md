---
title: 红魔9s Pro
date: 
tag:
- Life
- App
---

为了鹰角的终末地，考虑换手机。之前用的是Sony xperia 1 II, 骁龙865的芯片，担心性能不够。某天起床后，Sony的屏幕碎了一角，到公司后才发现有裂缝从屏幕的顶到脚。那这样就不得不考虑换手机了。

## 选手机
依照使用惯性，新手机还是需要ROOT使用，最好还是要刷类原生的系统，完整的屏幕，最好有耳机孔。那剩下的可选项就不多了：

1. Sony Xperia 1 V
2. 红魔9系列

当时知道就这2个，Sony一直是开放解锁BL的，红魔9是之前看到过泄漏工厂工具。

对比了性能，决定下单了红魔9。在挑选具体型号的时候，考虑到电池损耗，选择了出场较后的红魔9spro.

## 第一次收到手机
在淘宝多家店铺对比后，选择了一家号称是展柜机的店铺。下单一周后才发货。耐心等待收到快递后，拆开发现型号不对，收到的是华为的。联系卖家后，卖家表示是发错了，然后寄回去换货。拆快递的时候没有录像，担心是卖家做局。

## 收到手机
第二次收到快递后，特地录像了开箱，机器是红魔9s Pro。外观还行，但是风扇上有明显的灰尘。

然后开机，初始化进入系统，功能没问题。除了前置屏后摄像头在白色的背景下还是可以看到，褐色壁纸就基本上看不到了。

## 刷入LineageOS
到手体验下自带系统后就开始解锁刷机了。买手机前就在[xda](https://xdaforums.com/t/rom-android-16-beta-ota-lineageos-23-1-for-red-magic-9-pro-tiro.4763210/)上看到了可以刷LineageOS。按照[教程](https://wiki.itsvixano.me/devices/tiro/)解锁BL,刷入系统。

然后就被Android 16的界面丑到了。刷入Magisk和LSP,开始找自定义界面的工具。然后因为Android 16界面从xml改成了jetpack compose，导致的工具都不兼容了。

但是意外地找到了一个可以隐藏导航栏的工具[NavTweaks](https://github.com/DanGLVK/Hide-Navbar/issues/122)，搭配[Fluid Navigation Gestures](https://github.com/Coolboyrajat/Fluid-Navigation-Gesture)达到了和Sony上一样的自定义手势操作了。

受不了Android 16的界面圆角，尝试刷回REDMAGIC OS。找官网的系统，刷写后进了bootloader界面，系统不能启动。尝试刷入其他的版本，到了ZTE memory dump mode界面。

找了国际版的EDL刷机包，刷入后系统是进去了，但是指纹不能用。然后就想着刷会国行系统。

## 刷回REDMAGIC OS

找国行9008刷机就砖包的时候，发现资源都指向了[萤火虫资源站](https://www.yhcres.top)，但是上面的资源因为OneDrive云存储被封禁，资源都在123云盘上，网站上没有资源。可以在123云盘上看到红魔9s Pro的刷机包。

因为快过年了，所以就等过年再处理。红魔暂时装了游戏用来分担使用。

年后，萤火虫资源站重新上传了红魔9s Pro的9008线刷救砖包。

刷回国行系统后，指纹还是不能用。使用体验上带了国行特有的安全组件。没有比国际版更好的体验。决定刷回国际版系统。

## 国际版REDMAGIC OS

刷会国产版系统后，国产应用没有了，但是Google的那堆应用又回来了。直接停用不需要的应用，先用着。