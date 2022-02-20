---
title: install EFB on Arch
tags:
  - Code
date: 2019-03-03 16:58:51
---

自己用的两台笔记本都是`Arch Linux`，`VPS`上面图方便用的是`Ubuntu`。

之后用了段时间，发现并不好用。系统带的东西比较多。安装新东西需要手动添加源并解决依赖冲突的问题。包管理上不如`pacman`好用。
<!--more-->

# 重装 VPS

在`Vultr`上安装`Arch Linux`比较简单，参考[官方的教程](https://www.vultr.com/docs/installing-arch-linux-on-a-vultr-server)就可以了。

其中网络配置部分遇到点麻烦，教程中说是要配置`静态网络`，实际上使用`DHCP`就可以了。

``` bash
systemctl enable dhcpcd@eth0
```

另外需要注意的是，默认`root`是不开放远程登录的，所以远程登录新建用于进行。日常维护也使用这个用户就可以了。
用户管理的内容参考[Users and groups (简体中文)](https://wiki.archlinux.org/index.php/Users_and_groups_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))。

# 安装 Python
`Arch Linux`的好处就是系统本身是不带`Python`的，参考[Python](https://wiki.archlinux.org/index.php/Python#Python_3)安装最新的`Python`，然后参考[Python/Virtual environment](https://wiki.archlinux.org/index.php/Python/Virtual_environment)运行虚拟环境。

安装`Python`虚拟环境

```bash
python -m venv venv
```

激活虚拟环境

``` bash
source venv/bin/activate
```

退出虚拟环境

``` bash
deactivate
```

之后`Python`依赖和`EFB`的安装都在虚拟环境中进行。

# 安装 EFB

之后的安装配置`EFB`没有区别。

激活虚拟环境后，用`screen`运行`EFB`并返回前台后。断开`ssh`或者退出虚拟环境都不会中断`EFB`的运行。
如果运行时缺少依赖就安装对应的`Python`依赖就可以了。

# 升级系统

`Arch Linux`升级上面算是比较激进的。需要时不时地~~维护~~升级。