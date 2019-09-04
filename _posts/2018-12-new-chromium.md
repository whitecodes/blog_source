---
title: new chromium
date: 2018-12-12 21:56:12
tags:
- Life
- Code
---
`Google` 推出了新的`Material Design`，大量的圆角。不好看。`chromium`渐渐的也用上了圆角的设置。主要集中在标签和地址栏。

更新 `chromium` 71后，UI 固定成丑陋的圆角。这里记下中间处理这个UI的过程

最后的方案是回滚旧版本
<!--more-->
# 妄想
`chromium`本身是开源的，于是就想有没有人fork并修改了UI。找了一圈，虽然找到个很多的抱怨，但是修改源码重新打包的？没有。

于是开始不知量力的看看能不能自己编译版本。找到了[官方的编译说明](https://www.chromium.org/developers/how-tos/get-the-code)，看了一遍在`Linux`下的编译，放弃了……

# 替代品
既然不能自己编译就找找其他基于`chromium`的浏览器

## FireFox
首先试的就是和`chrome`对位的`firefox`。安装了`FireFox Developer Edition`。默认黑色的主题，看起来还行，至少没有`Material Design`那么圆润。然后开始自定义界面，关掉不需要功能。最显眼的就是要关掉`Pocket`。设置里各种设置，地址栏上的就是关不掉。

`Ghost`提供建议可以试试`ESR`，用`policies.json`来关闭附加的`Pocket`。

当晚太晚了，计划第二天去试试。

## Vivaldi
第二天决定去试试`vivaldi`。
>Vivaldi is a new web browser from former Opera founder & team members, based on Chromium and focused on personalization aspects.

基于`chromium`开发的，而且颜值不低。

安装`vivaldi`，然后开始自定义设置。界面上把各种附加的笔记之类的功能全部隐藏掉，眼不见心不烦。常用的`chromium`扩展也都安装上了。试用下来功能没问题，就是和`chromium`比，稍稍慢一点，特别是新建标签页的时候。
但是这个还可以接受。~~毕竟颜值高~~

比较烦人的地方就是，proxy 经常失效。

## Opera
最后想要尝试下`opera`，毕竟换内核之前也是很好看的，当初界面也是很有特色的。

下载安装了。打开迎面而来的是浓浓的国产风，各种预制的书签和链接。努力在设置里关闭了附加功能，新标签页还是`百度`。

界面看起来还行，不算特别丑。看着界面，感觉有点像`Edge`。

# chromium 降级
剩下的方案就是`chromium`降级了。我知道`pacman`可以设置忽略某些包的升级，但是不清楚是不是支持降级或者安装制定版本的。

[Arch Wiki](https://wiki.archlinux.org/index.php/Downgrading_packages_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))上有写了是支持的，还有降级的软件/脚本

使用`yay`安装`downgrade`，然后输入命令进行降级
```
downgrade chromium
```
在列出的版本中选择本地的版本，远程的会签名验证不通过

依赖关系方面，`chromium`是独立的不用降级其他软件。

# chromium 71 之前的版本的处理
`chromium` 70的版本默认界面还是`Material Design`风格的，输入`chrome://flags#top-chrome-md`，修改为`Normal`，重启`chromium`后就是原来的界面了。

由于是降级，每次开启`chromium`都会提示：资料来着高版本什么的。按照网上的方法就基本使用正常

1. 关闭`chromium`
2. 删除`~/.config/chromium/Default/Web Data`
3. 启动`chromium`，重新登录账户
