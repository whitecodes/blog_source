---
title: 配置Netdata
tags:
  - Code
date: 2022-02-06 22:59:00
---
`Netdata`是一个用来监控服务器的软件，我用来监控`VPS`和`OMV`的运行情况。
这个软件的配置项比较多，这里记录下配置。
另外默认的告警也有很多，这里顺便记录下碰到的告警的解决办法

<!--more-->

## DOCKER安装Netdata

用`docker`安装比较好管理，也比较好升级。官方有[教程](https://learn.netdata.cloud/docs/agent/packaging/docker),基本上跟着来就可以了。

其中需要注意的一点是权限问题：`Netdata`会固执地使用`netdata`用户来运行。如果碰到权限问题不能运行，想要修改`Netdata`的运行权限，除了需要在运行的时候指定用户，还需要在环境变量中指定用户。

## 配置告警

`Netdata`支持很多告警方式，我用的是`Telegram`通知。

根据[官方的文档](https://learn.netdata.cloud/docs/agent/health/notifications/telegram)需要一个`Telegram Bot`。

和`BotFather`对话就可以创建一个`Telegram Bot`。把这个bot加到群组中，需要获取`chat ID`，方法在文档中有。

在主机上找一个位置创建告警的配置文件，命名为`health_alarm_notify.conf`，之后要把文件这件挂载到容器中`/etc/netdat/health_alarm_notify.conf`。

文件内容大概是这样：

```conf
SEND_TELEGRAM="YES"
TELEGRAM_BOT_TOKEN="111122223:7OpFlFFRzRBbrUUmIjj5HF9Ox2pYJZy5"
DEFAULT_RECIPIENT_TELEGRAM="-100233335555"

```

挂载后重启容器就可以开启告警了。

<div class="warning">

> __TODO__ _测试告警_

</div>

## 解决告警

告警默认是全开的，用在`VPS`上的话，有些值的设置有些不合理。例如`30min_ram_swapped_out`这个，默认值设置得很低，经常会触发。

创建文件`health.d/swap.conf`，挂载到容器中的`/etc/netdata/health.d/swap.conf`。

文件的原始内容你可以从[这里](https://github.com/netdata/netdata/blob/master/health/health.d/swap.conf)找到，主要要修改告警的值。文件内容大概是这样：

```

# you can disable an alarm notification by setting the 'to' line to: silent

    alarm: 30min_ram_swapped_out
       on: system.swapio
    class: Workload
     type: System
component: Memory
       os: linux freebsd
    hosts: *
   lookup: sum -30m unaligned absolute of out
           # we have to convert KB to MB by dividing $this (i.e. the result of the lookup) with 1024
     calc: $this / 1024 * 100 / ( $system.ram.used + $system.ram.cached + $system.ram.free )
    units: % of RAM
    every: 1m
     warn: $this > (($status >= $WARNING)  ? (85) : (95))
    delay: down 15m multiplier 1.5 max 1h
     info: percentage of the system RAM swapped in the last 30 minutes
       to: sysadmin

    alarm: used_swap
       on: system.swap
    class: Utilization
     type: System
component: Memory
       os: linux freebsd
    hosts: *
     calc: (($used + $free) > 0) ? ($used * 100 / ($used + $free)) : 0
    units: %
    every: 10s
     warn: $this > (($status >= $WARNING)  ? (80) : (90))
     crit: $this > (($status == $CRITICAL) ? (90) : (98))
    delay: up 30s down 15m multiplier 1.5 max 1h
     info: swap memory utilization
       to: sysadmin
```

如果硬盘比较小，并且进行过大容量的占用，那么会触发一个`btrfs allocated too high`的告警，这个告警不会在删除大文件后消除，需要跑一遍`btrfs balance`来消除。

## 配置fail2ban

`VPS`使用`fail2ban`来防护，可以在`Netdata`上看到相关的数据。

根据[官网上的教程](https://learn.netdata.cloud/docs/agent/collectors/python.d.plugin/fail2ban)：`Netdata`是通过读取`fail2ban.log`来获取的。所以需要确保这个文件可以被`Netdata`读取，默认加`Netdata`用户的读取权限就可以了。

创建文件`python.d/fail2ban.conf`挂载到容器中的`/etc/netdata/python.d/fail2ban.conf`。

文件内容大概是这样：
```
local:
  log_path: '/var/log/fail2ban.log'
  conf_path: '/etc/fail2ban/jail.local'
```

重启容器后就可以看到`fail2ban`的数据了。其中主要关心的是`Banned IP addresses`。这个数据是`Netdata`启动后的数据。如果要准确的，那就容器启动后重启下`fail2ban`服务。

## 配置温度

`VPS`上没有温度相关的，但是用到自己的`NAS`上的时候，还是需要关注下机器的温度。

创建文件`charts.d.conf`挂载到容器中的`/etc/netdata/charts.d.conf`。

文件内容大概是这样：

```
sensors=force
```

重启容器后就可以看到`sensors`的数据了。

其实温度看的还是硬盘的问题，所以更好的处理还是要看`S.M.A.R.T.`的数据。

<div class="warning">

> __TODO__ _硬盘S.M.A.R.T_

</div>

## 配置串流

<div class="warning">

> __TODO__ _还没做_

</div>