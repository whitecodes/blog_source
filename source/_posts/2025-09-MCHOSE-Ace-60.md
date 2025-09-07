---
title: 迈从Ace60pro键盘
date: 2025-09-7 15:32:30
tag:
- Life
---
新买了键盘，磁轴的，过程比想象中曲折。
<!--more-->

## 缘起
之前公司用的是 IKBC C87 茶轴，87键的，和家里用的60键盘布局不一致，就想着全部换成60的键盘。
首先是把poker键盘拿去了公司，家里用C87。但是家里用不习惯，还是决定买一把新的。

## 狼蛛mini60
搜索了晚上最便宜的60配列的键盘，为了保持Fn功能一致，要么低端磁轴要么自组机械键盘。
磁轴支持驱动改键，自组可以自己编译刷驱动。
磁轴上不考虑极端键程的性能，最具有性价比的就是狼蛛的mini60了：磁轴支持网页驱动改键，60配列，三模连接，还有RGB背光。
于是就是蹲了几天119下单了。买的是黑色侧刻搭配风云磁轴，收到的是键帽是等高线侧刻的，我赚了。新键盘带RGB等高线好看。
然后连接电脑连驱动，公司驱动页面打不开，狼蛛官网被归类到游戏网站了。家里Linux的环境中，驱动页面无反应，没有选择键盘的弹窗。F12看是有个请求404了。
搜了下，Linux 环境中的chrome内核浏览器是支持webhid功能的，但是需要手动给权限。通过访问[device-log](chrome:device-log)页面可以看到尝试键盘的hid设备名称，然后执行命令给权限。

```bash
sudo chmod o+rwx /dev/hidraw3
```

给了读写权限后，驱动页面可以选择键盘了，但是报错不支持的设备。和客服拉扯一阵后，网页驱动只支持windows环境。
去网吧连接键盘后，网页驱动可以正常使用。修改按键的时候，发现Fn键没有办法修改到其他按键上。找客服确认，Fn键不支持修改。

现在就有2个选择了：
- 选择1：留下新键盘，修改poker上的Fn的位置并重新适应新布局
- 选择2：退货，买新键盘

最后还是选择了退货。

## 迈从Ace60

重新寻找键盘，这次重点查看了Fn键的位置和驱动修改范围。
迈从Ace的Fn在左下，问了客服确认Fn键可以修改，也找到了驱动页面上Fn键的截图。但是迈从的只有有线连接。

美加狮mad60的Fn位置和poker的一样。但是看驱动页面，Fn层系统键位是不能修改的。虽然reddit上[有人可以改](https://www.reddit.com/r/MechanicalKeyboards/comments/106060u/madcatz_mad60_can_i_change_fn_layer/)，但是方法看来更像是利用了驱动交互的漏洞。

更高的价格上，就可以直接上自组机械键盘了。
对比下，买了迈从Ace 60 pro 蓝冰磁轴。有线就有线吧，poker也是有线的。

收到了键盘，插线开灯，连网页驱动。公司可以访问网页驱动，Fn键可以修改。RGB的效果因为是上灯位，再加上键帽只是黑色正刻透光，灯光效果一般，即使之后换上了等高线侧刻，只有键盘的上半部分是透光的，效果还是不如狼蛛。

另外就是原厂键帽的品控问题：右Shift键是外的，左侧有些靠下。换了键帽解决这个问题。

poker的Fn层布局可以完整复制到新键盘上。可以用了。
回去后，Linux环境中，驱动页面也可以用了，好评。

总结下购买建议：
如果追求60配列，可以接受Fn在左下角不能修改，那么推荐狼蛛mini60;
可以接受Fn层系统键位不能修改，那么推荐美加狮mad60;
如果可以接受有线连接，那么推荐迈从Ace60。
更高的价格上，就可以直接上自组机械键盘了。

顺便还体验了下按键宏，输入设备的密码很方便。
于是也有了之后poker编程键位

## IKBC new poker II

poker键盘是支持全键盘编程键位了，并且可以存储三层。但是之前没有工作使用，所有也没有进行编程，最开始的常规层就够用了。

需要说明的是，这里绑定的密码是测试设备的密码，这个密码不重要的。**重要的密码不要存储在键盘宏中**

具体编程的方法查看[用户文档](https://github.com/tsl0922/IKBC)。

出场的版本是1.02.00。尝试将密码绑定到Q键上，没有报错（也没有地方可以看到报错），但是没有生效，Q键直接不输入任何内容。但是可以将单个键位编程成其他键位。

升级到1.03.00后，就可以将Q键编程成输入密码了。

之后找到了1.03.03的固件，升级后可以将宏绑定到Fn+Q上。

现在日常就是使用可编程的layout1，Fn+Q是快速输入密码。其他宏还没有设置。
唯一的问题就是现在有个表示层数的红灯常亮。

另外在找固件的过程中发现有人为Vortex POK3R适配了[QMK](https://github.com/pok3r-custom)。但是这款键盘淘宝上找不到出售的，估计停产了。
关于Vortex和IKBC的关系，可以参考AI的总结：

```
Poker Series Evolution Timeline
--------------------------------

2010 ~
  └── Poker (Vortex)
        - Minimal 60% layout
        - Pure Fn-layer navigation

2012
  └── Poker II (Vortex)
        - Added DIP switches
        - Basic programmability

2014/2015
  └── Pok3r (Vortex)
        - Full on-board programmability (3 layers + macros)
        - Optional metal case
        - Popular in custom community

2015/2016
  └── Team Split (Vortex ↔ iKBC)
        |
        ├── Vortex Line
        |      └── Pok3r RGB (later expansion, lighting support)
        |
        └── iKBC Line
               └── New Poker II (2016/2017)
                      - Plastic case (lighter)
                      - PBT keycaps stock
                      - DIP switches, limited programmability
                      - Aimed at mass retail / affordability
```

### 参考资料
- [说明书&1.03.00固件](https://github.com/tsl0922/IKBC)
- [1.03.03固件&版本说明](https://www.dropbox.com/s/udyrb71p14uxo2s/New%20Poker%20II%20Firmware%201.03.03.zip?dl=0)
- [救砖相关](https://geekhack.org/index.php?topic=50245)