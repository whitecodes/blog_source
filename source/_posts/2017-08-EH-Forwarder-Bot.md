---
title: EH Forwarder Bot
date: 2017-08-21 01:05:49
tags:
- Code
- App
---
一直不喜欢`微信`，但是周围人都在用。找到的解决办法是用`EH Forwaarder Bot`把`微信`转发到`Telegram`上。这样就就可以安心的把`微信`放到冰箱里了

<!--more-->

# 申请 VPS

`EFB`需要一台长期在线的服务器来访问`微信`网页，于是用`VPS`是个好主意。
`VPS`我选择了`Vultr`家的，原因么……便宜。
注册`Vultr`帐号，用`Paypal`充值，新建最便宜的`VPS`。用`SSH`登录之后这部分就算完成了。
为了避免依赖的处理，安装官方的文档选择`Ubuntu`系统。
# 安装 EFB

接下来就是在`VPS`上安装`EFB`和相关依赖。

```Bash
sudo apt-get install python3-dev python3-setuptools
sudo apt-get install libwebp-dev
sudo apt-get install libmagic-dev ffmpeg

git clone https://github.com/blueset/ehForwarderBot.git
cd ehForwarderBot
pip3 install -r requirements.txt
```

# 设置 Telegram Bot
`Telegram`上需要注册一个`Bot`来和`EFB`通信。注册的方法很简单，和`@BotFather`对话就可以了。这里直接看[网上的教程](https://blog.1a23.com/2017/01/09/EFB-How-to-Send-and-Receive-Messages-from-WeChat-on-Telegram-zh-CN/)

在`VPS`上创建存储`微信`的地方，在`ehForwarderBot`下执行
```Bash
mkdir storage
chmod +x storage
```

接下来还要配置`EFB`
```Bash
cp config.sample.py config.py
nano config.py
```
修改文件的最后一段，填入新建`Bot`时得到的`token`和`Telegram ID`
看起就像这样
```json
eh_telegram_master = {
    "token": "12345678:QWFPGJLUYarstdheioZXCVBKM",
    "admins": [123456789],
    "bing_speech_api": ["3243f6a8885a308d313198a2e037073", "2b7e151628ae082b7e151628ae08"],
    "baidu_speech_api": {
        "app_id": 0,
        "api_key": "3243f6a8885a308d313198a2e037073",
        "secret_key": "2b7e151628ae082b7e151628ae08"
    }
}

```

# 使用

在`VPS`上启动`EFB`

```Bash
python3 daemon.py start
```
启动微信扫描屏幕上的二维码进行授权。
显示：

>Start auto replying

时，即代表登陆成功
