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

```

```

## 配置告警

## 解决告警

## 配置fail2ban

## 配置温度

## 配置串流

<div class="warning">

> __TODO__ _还没做_

</div>