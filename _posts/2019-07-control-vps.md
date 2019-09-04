---
title: control vps
tags:
  - Code
date: 2019-07-21 23:38:15
---

关于`VPS`的一些操作记录下，类似目盲下的流水帐
<!--more-->
# 更新Hexo

很久没写`blog`了，想写点东西，发现`hexo`命令无效了。大概是之前用`yarn`安装的，后来删除了就无效了。重新使用`npm`安装后解决。

# 重新安装VPS

之前安装的`Arch Linux`因为太久没有登录`VPS`，密码忘记了，就索性重装了系统。系统还是`Arch Linux`，把密码另外记下来了，大概不会出现忘记的情况了。

# EFB

照例还是安装了`EFB`，毕竟这个才是`VPS`的主要用途。最新版本会在手机端登录微信的时候收到一条不支持的消息，不知道是什么。其他大图片发不出去之类的情况还在。但是基本的聊天使用还是没有问题的。

# fail2ban

昨天好奇看了登录记录，发现5W+的登录失败记录。研究了下`fail2ban`。发现这是个分析登录日志来添加防火墙规则的工具。装是装了，但是还没有启用。现在的设置问题也不大。

# Fava & beancount

一直在找合适的记账工具，`ghost`推荐了`beancount`。使用文本记录的复式记账。这样的话PC端和移动端就可以同时记账了。

## 新建beancount环境

`beancount`是`python`的一个软件，不期望和其他的`python`混在一起，这里使用`python虚拟环境`。
```bash
mkdir beancount
cd beancount
python -m venv venv
```

启用虚拟环境
```bash
source venv/bin/activate
```

之后的启动等操作都在虚拟环境中完成

推出虚拟环境
```bash
deactivate
```

## 安装 beancount & Fava

```bash
pip install beancount
pip install fava
```

## 建立账本文件

记账就是按照`beancount`的格式直接写入文件里就可以了。具体的文件结构看自己需要了。

入口文件

```beancount
; 设定账本标题
option "title" "beancount"

; 设定账本主货币
option "operating_currency" "CNY"

; 所有使用中的账户都写在 accounts.bean
include "accounts.beancount"

; 每年的账本
include "2019.beancount"
```

启动`Fava`
```bash
fava --host {hostip} --port {port} main.beancount
```

更多关于`beancount`和复式记账的内容查看[把自己当做一家公司：使用 beancount 记账](https://lyric.im/c/beancount-tutorial/)