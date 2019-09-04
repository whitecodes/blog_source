---
title: EH Forwarder Bot 2.0
date: 2018-02-25 16:37:14
tags:
- Code
- App
---
`EFB`升级到`2.0`了，把过程记录下。
<!--more-->
# 原本存在的问题
原本的VPS网络链接不好的时候，会累积错误，10个就回报一次，搞得`Telegram`上都是错误信息。其实这个错误没什么就是看着烦。`EFB`出了2.0版本就想着升级下。

# 黑域和绿色守护
原本是打算放弃`EFB`的，毕竟这种用`微信网页版`接口的迟早会被干掉的。尝试着用其他的工具来控制。但是不管是用`黑域`还是`绿色守护`，都没有消息提醒。于是还是回到`EFB`+`icebox`的组合

# 安装
2.0的`EFB`把 Master 和 slave 分开了，安装上稍稍有点不同。

## 安装缺少的依赖
系统还是用了`Ubuntu`。
可以通过`PyPi`安装`EFB`了，先安装`pip3`。
```Bash
apt install python3-pip
```

系统不同安装的依赖名称可能也会不同
```Bash
apt install ffmpeg libmagic-dev libwebp-dev
```

这两个`python`工具不知道为什么没有，先装上
```Bash
pip3 install setuptools wheel
```
其他的依赖在安装本体的时候可以自动装上

## 安装本体

安装`EFB`框架
```Bash
pip3 install ehforwarderbot
```

安装`EFB Telegram Master Channel (ETM)`
```Bash
pip3 install efb-telegram-master
```

安装`EFB WeChat Slave Channel (EWS)`
```Bash
pip3 install efb-wechat-slave
```
`Github`上作者还提供了`EFB Facebook Messenger Slave Channel (EFMS)`。我不用，就不安装了。

## 准备文件夹和配置
根据文档，需要手动建立配置存放的文件夹~~可能不用~~。这里安装文档的说明来
```Bash
cd ~
mkdir .ehforwarderbot
```
框架的配置文件路径有点深,不想手动建就先跑一下`EFB`，命令`ehforwarderbot`。会报错，但是路径已经有了。
```Bash
cd ~/.ehforwarderbot/profiles/default/
nano config.yaml
```
习惯用`nano`了，不用学`vi`的语法。

只使用`ETM`和`EWS`的话，照抄下面的就可以了
```Yaml
master_channel: blueset.telegram
slave_channels:
- blueset.wechat
```

上面是`EFB`框架的配置，接下来配置`ETM`
默认的配置文件是`~/.ehforwarderbot/profiles/default/blueset.telegram/config.yaml`,如果没有需要手动建立

```Yaml
token: "123456789:AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA"
admins: 123456789
flags:
    chats_per_page: 20
    network_error_prompt_interval: 250
```
具体的配置内容看`ETM`的[Github](https://github.com/blueset/efb-telegram-master)

`EWS`也有配置项，但是看看都用不到就不配了。

# 后台运行
>Since version 2, EH Forwarder Bot has removed the daemon helper as it is unstable to use.

需要新的方法后台运行`EFB`，但是我又不想开机启动，而且`EFB`本身还要有二维码显示，我选择使用`Screen`保持`EFB`后台运行
```Bash
screen ehforwarderbot
```
登录之后要关闭`SSH`，可以将`EFB`后台：Ctrl+a、Ctrl+d。之后再退出`SSH`，`EFB`会一直在后台运行。

回到`EFB`可以使用下面的命令。下面是个网上的例子
```Bash
[root@tivf06 ~]# screen -ls
There is a screen on:
        16582.pts-1.tivf06      (Detached)
1 Socket in /tmp/screens/S-root.
[root@tivf06 ~]# screen -r 16582
```

# 更新
已经用`pip3`来安装了，那更新也用`pip3`就方便多了
```Bash
pip3 install -U ehforwarderbot
pip3 install -U efb-telegram-master
pip3 install -U efb-wechat-slave
```