---
title: 媒体服务器
date: 2019-01-13 21:52:36
tags:
- Code
- Life
---
手上有台`VPS`一直闲置，考虑搭建个媒体服务，用来在线看看视频。
<!--more-->
# Plex
在网上搜索  
首先想到的计划就是安装`Plex`。但是看了教程，发现首次启动需要本地或者端口转发。不想折腾工具就算了。

# Emby
找到了竞品`Emby`。效果差不多。安装按照官方的教程走就可以了。
下载安装包，然后使用`dpkg -i emby-server-deb_3.5.3.0_amd64.deb`安装就可以了。安装完会自动启动后台。
浏览器打开[http://localhost:8096](http://localhost:8096)就可以进行初始化了。

尝试在线看视频，发现使用web在线看是需要收费的……

# Jellyfin
后来在无意中找到了开源版本`Jellyfin`。看起来还行，尝试按照教程安装，错误一堆……

## 安装 Jellyfin

`VPS`装的系统是`Debian`，官方源里没有`Jellyfin`。 添加源并安装

``` sh
sudo echo `deb https://repo.jellyfin.org/debian stretch main` > /etc/apt/sources.list.d/jellyfin.list
sudo apt update
sudo apt install jellyfin
```

没有错误的话，就安装好了。浏览器访问[http://localhost:8096](http://localhost:8096)就可以进行初始化。

语言可以选简体中文，但是不能完成设置，页面一直在转圈。

重启服务之后正常。

接下来添加媒体库，指定文件夹。指定的文件夹需要可以被`jellyfin`用户组访问

试了在线观看，还可以

在线播放搞定了，接下来就是下载的问题了