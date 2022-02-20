---
title: 坏日子
date: 2017-01-11 13:36:38
tags:
- Life
- Code
---
周二早晨醒来开电脑。黑屏……然后重启后分辨率回到了最小值。看`设备管理器`显卡上两个黄色的感叹号。显卡坏了。
<!-- more -->
# 电脑坏的第一天
## 重装驱动
第一反应是要重装驱动。但是已经打开了`设备管理器`就看看系统怎么说：
> Windows has stopped this device because it has reported problems. (Code 43)

有错误代码就有方向了，把错误代码扔给`Google`。出这种问题的肯定不止我一个。`Google`给出的解决办法是重装驱动……
那就重装驱动吧~下载……卸载……安装……
问题依旧！
## 更严重的问题
在重装驱动的过程中出现了更严重的问题：每隔一段时间就会直接关机。这简直就是处于完全不能用的状态。
怀疑是系统的问题。趁着可以使用的一小段时间备份了`C盘`的重要数据就开始重装系统。
重装完，安装显卡驱动。显卡问题依旧，基本可以确定是硬件问题了。~~那就基本无解了，我毕竟不是硬件专业的。~~`C盘`被格式化了，于是重新安装部分软件。下载`Shadowsocks`需要的`.Net`的时候，又黑屏管理了……~~我要拆了它！~~
## 拆机装机
找了螺丝刀开始拆机。基本上慢慢来，每一步之前仔细观察，不要`大力出奇迹`什么的就基本没问题。
全部拆开之后，清清灰，又重新装起来。
拆的时候没有拍照，于是有一颗螺丝死活记不起来是哪里的。找了合适位置随便按上了，内心祈祷不要出什么问题。
## 基本使用
正常开机，安装完`Chrome`都没有什么问题。显卡问题依旧，勉强用着吧。游戏是不指望了，视频画质勉强能看。
拖延症发作，明天再装开发需要的软件好了。

# 电脑坏的第二天
前端开发用的软件就`Cmder`和`Sublime Text`。
## Cmder
`win7`自带的命令行工具实在不好用。`Cmder`有包含`Git`的安装包。下载回来才发现`Cmder`的安装包就是程序的压缩包。`D盘`有未压缩的程序。我直接打开,提示找不到`SSH`相关文件。我才记起还有生成`SSH Key`。
安装`chocolatey``node.js``Hexo`，按提示重启`Cmder`,要权限就给。
## Sublime Text 3
`Cmder`安装软件我基本都记在{% post_link build-by-hexo build-by-hexo %}里了，但是`Sublime Text`安装的插件我没有记录。
### Package Control
`Sublime Text`的包管理工具。通过`Sublime Text console`安装。跟着[官网](https://packagecontrol.io/installation)走很快的。

### 安装插件
通过`Package Control`安装其他插件就方便了。趁着这个机会也记录下。
+ ChineseLocalizations
+ Theme - Soda SolarizedDark
+ Omni​Markup​Previewer
+ Vue Syntax Highlight

# 电子设备的命运
感觉电子设备到我手上总是特别凄惨。电脑长时间不关机。手机每次出门都会用到电量耗尽。四周边角不管是电脑还是手机都是磕磕碰碰的`垃圾场成色`。