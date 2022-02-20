---
title: 在 XPS 15 9560 上安装 Arch Linux
date: 2018-07-16 21:54:44
tags:
- Code
---
记录下安装`Arch Linux`的大致过程

__Nvidia 显卡没有安装成功__
<!--more-->

安装流程大致参考了`wiki`上的[Installation guide](https://wiki.archlinux.org/index.php/Installation_guide)(可选中文简体)
和`中文论坛`的[安装指南](https://bbs.archlinuxcn.org/viewtopic.php?id=1037)

# 准备安装介质
使用U盘重装系统，需要准备一个空白的U盘，确切来说是一个你不需要里面数据的U盘。我下载的时候安装镜像的大小是571MB。

从[Arch Linux Downloads](https://www.archlinux.org/download/)下载安装镜像。推荐使用`BitTorrent`下载，也可以直接通过下载页面下方的中国的镜像下载。建议下载完成之后校验下。

`Linux`环境有`dd`命令可以直接制作安装U盘。[详细说明](https://wiki.archlinux.org/index.php/USB_flash_installation_media#Using_dd)
```Bash
dd bs=4M if=/path/to/archlinux.iso of=/dev/sdx status=progress oflag=sync
```

`windows`环境也有对应的工具。印象里直接用`微软`官方的[Windows USB/DVD Download Tool](https://www.microsoft.com/en-us/download/windows-usb-dvd-download-tool)就可以制作安装U盘。具体的步骤可以参考[制作Win7安装U盘的过程](http://bbs.ngacn.cc/read.php?tid=4025346)

# UEFI设置
根据`wiki`上关于`XPS 15 9560`的[相关说明](https://wiki.archlinux.org/index.php/Dell_XPS_15_9560#UEFI)，安装前需要修改一些`UEFI`设置。

在`Windows 10`中可以通过下面的方法进入`UEFI`。
__TODO__~~忘记这个选项在哪里了~~

+ 将硬盘的`SATA Mode`从默认的`RAID`改为`AHCI`。如果需要继续进入已有的`Windows`，那么需要看看这个[SOLUTION](https://triplescomputers.com/blog/uncategorized/solution-switch-windows-10-from-raidide-to-ahci-operation/)
+ 将__TODO__~~忘记是什么了~~从`Thorough`改成`POST Behaviour`
+ 关闭`secure boot`

__我没有安装双系统，后面的设置都是安装`Arch Linux`单系统来说明的__
# 安装环境配置
插上U盘，重启。在出现`Dell`的图标的时候一直不停按`F12`，直到出现进度条。之后就进入`Boot Menu`，选择带`USB`字样的选项，进入安装环境。

屏幕是`4K`的分辨率，默认字体小，看着要眼瞎。所以先改字体
```Bash
setfont latarcyrheb-sun32
```

安装过程需要联网
```Bash
wifi-menu
```
通过`ping`确保网络
```Bash
ping t.cn -c 4
```
`baidu`现在连确定网络的功能都没有了

同步时间
```Bash
timedatectl set-ntp true
```

选择软件仓库服务器，用国内的镜像下载速度会快很多
```Bash
sed -i '/China/!{n;/Server/s/^/#/};t;n' /etc/pacman.d/mirrorlist
```

本来还需要验证启动模式，但是我这里确定是`EFI`就不写了，要看可以去看前面的参考。

# 分区&格式化
用的是`EFI`，那就需要一个`EFI 系统分区`。`Arch Linux`本身需要一个分区分配给根目录`/`。以防万一计划分一个内存大小的`SWAP`分区

使用`lsblk`确定SSD的磁盘名称
```Bash
lsblk
```
结果中带'nvme'字样的就是笔记本的SSD。其他以'loop'结尾的可以忽略。

然后使用`parted`或者`cfdisk`进行分区。安装的是`Arch Linx`单系统，直接删除原有的分区表，直接新建就可以了。具体过程可以看参考中的GIF

| 分区      |  大小  | 格式 | 路径 |         说明 |
|:----------|:------:|:----:|:----:|-------------:|
| nvme0n1p1 |  512M  |  -   |  -   | EFI 系统分区 |
| nvme0n1p2 |  16G   |  -   |  -   |     交换分区 |
| nvme0n1p3 | 160.5G |  -   |  -   |       根分区 |   

首先格式化`EFI 系统分区`，根据wiki上的[EFI 系统分区](https://wiki.archlinux.org/index.php/EFI_System_Partition)
```Bash
mkfs.fat -F32 /dev/nvme0n1p1
```

格式化交换分区
```Bash
mkswap /dev/nvme0n1p2
swapon /dev/nvme0n1p2
```
格式化根分区，用的还是传统的`EXT4`
```Bash
mkfs.ext4 /dev/nvme0n1p3
```

格式化完成后使用`lsblk`查看有没有错

| 分区      |  大小  |  格式  |  路径  |         说明 |
|:----------|:------:|:------:|:------:|-------------:|
| nvme0n1p1 |  512M  | Fat32  |   -    | EFI 系统分区 |
| nvme0n1p2 |  16G   | [SWAP] | [SWAP] |     交换分区 |
| nvme0n1p3 | 160.5G |  EXT4  |   -    |       根分区 | 

# 安装系统
安装前先挂载分区到对应路径
```Bash
mount /dev/nvme0n1p3 /mnt
mkdir -p /mnt/boot/EFI
mount /dev/nvme0n1p1 /mnt/boot/EFI
```
使用`lsblk`查看有没有错

| 分区      |  大小  |  格式  |     路径      |         说明 |
|:----------|:------:|:------:|:-------------:|-------------:|
| nvme0n1p1 |  512M  | Fat32  | /mnt/boot/EFI | EFI 系统分区 |
| nvme0n1p2 |  16G   | [SWAP] |    [SWAP]     |     交换分区 |
| nvme0n1p3 | 160.5G |  EXT4  |     /mnt      |       根分区 | 

安装基础软件包组`base`和基础开发包组`base-devel`，后者用于安装`AUR`中的软件
```Bash
pacstrap -i /mnt base base-devel
```
如果有选项默认回车就可以了。
如果之前修改软件仓库服务器用了国内的镜像，安装很快就会完成。

至此，`Arch Linux`可以算是安装完成了。但是并不能作为日常使用。接下来需要进行基本的配置和安装必要的软件，包括桌面环境
# 基本配置
生成分区表。检查一下生成的文件是否正确
```Bash
genfstab -U /mnt >> /mnt/etc/fstab
cat /mnt/etc/fstab
```

使用`arch-chroot`进入新系统。接下来的配置都在新系统里进行
```Bash
arch-chroot /mnt
```

设置时区
```Bash
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

同步硬件时间
```Bash
hwclock --systohc
```

区域设置
```Bash
nano /etc/locale.gen
```
去除`en_US.UTF-8 UTF-8`和`zh_CN.UTF-8 UTF-8`前的`#`，保存退出`nano`
生成`locale`
```Bash
locale-gen
```
设置默认 locale
```Bash
echo 'LANG=zh_CN.UTF-8'  > /etc/locale.conf
```

主机名设置，这里直接设置为`xps`
```Bash
echo xps > /etc/hostname
nano /etc/hosts
```
输入以下内容，保存退出
```Hosts
127.0.0.1 localhost
::1 localhost
127.0.1.1 xps.localdomain xps
```

安装引导程序。这里直接使用传统的`GRUB`了
```Bash
pacman -S dosfstools grub efibootmgr
grub-install --target=x86_64-efi --efi-directory=/boot/EFI --bootloader-id=grub
grub-mkconfig -o /boot/grub/grub.cfg
```

设置`root`密码
__重要，必须记住的密码__
```Bash
passwd
```
按提示输入密码

日常使用的话，基于安全原因不使用高权限的`root`用户。新建用户作为日常使用。这里以`archie`作为用户名。
```Bash
useradd -m -g users -G wheel -s /bin/bash archie
passwd archie
```
按提示输入密码。日常使用就以`archie`用户配合设置的密码登录。日常使用中偶尔需要`root`权限，这里配置`sudo`用来临时提权
```Bash
EDITOR=nano visudo
```
找到`Uncomment to allow members of group wheel to execute any command`这行，删除下一行的`#`，保存退出。
关于`sudo`的用法和更多配置[参考](https://wiki.archlinux.org/index.php/Sudo)

# 桌面环境
尝试使用独立显卡，但是失败了。这里只记下了只使用核显的方法。如果跳过这一步会导致`XPS`开关机挂起
```Bash
nano /etc/default/grub
```
在`GRUB_CMDLINE_LINUX_DEFAULT`追加`acpi_rev_override=1`。加完之后该行差不多就是这个样子
```
GRUB_CMDLINE_LINUX_DEFAULT="quiet acpi_rev_override=1"
```

使用`xorg`作为图形界面的接口。这里偷懒直接全部安装了，反正存储不是特别紧张
```Bash
pacman -S xorg
```

不喜欢`gnome`，所以安装`KDE Plasma`，顺手装上中文语言包
```Bash
pacman -S plasma-meta kde-l10n-zh_cn
```

安装显示管理器`sddm`
```Bash
pacman -S sddm
```
`sddm`默认不需要配置，直接启用就可以了
```Bash
systemctl enable sddm
```

这样开机就能启动图形环境了，前面已经配置了中文环境，但是没有中文字体。不安装字体就会显示“豆腐”
```Bash
pacman -S noto-fonts-cjk noto-fonts-emoji
```

# 网络配置
`XPS 15 9560`没有网口，这里直接安装无线的相关包
```Bash
pacman -S iw wpa_supplicant dialog 
```
安装网络管理包，这里貌似要安装`gnome`的才有用，原因存疑
```Bash
pacman -S networkmanager
```

接下来重启进入新系统
```Bash
exit
reboot
```
这里关机重启`XPS`可能会挂起，长时间无响应。长按电源键直接关机。然后按电源键开机进入新系统。安装用的U盘可以拔掉了

进入系统之后，会发现显示的字体特别小，而且没有无线网络。按下`Ctrl`+`Alt`+`F2`，进入`tty2`，用`root`帐号登录。
先按照之前的步骤设置字体。

查看端口
```Bash
ip link show
```
显示的结果中带`wlp`项就是无线，这里以`wlp3s0`为例。上线无线端口，并开启管理服务
```Bash
ip link set wlp3s0 up
systemctl enable NetworkManager
```

按下`Ctrl`+`Alt`+`F1`，回到图形界面。
打开`系统设置`->`显示和监控`->`显示`，设置`缩放显示`到合适的大小，4K屏的`XPS`设置`250%`差不多。确定并应用。

然后重启，应该可以看到显示的字体可以看了，也可以在默认的右下角设置无线网络连接了。

# 安装必要软件
`Arch Linux`安装软件一般都需要连接网络。先连接网络。
现在图形环境没有终端，先安装终端。

切换到`tty2`，登录

安装`konsole`
```Bash
pacman -S konsole
```

安装完后，`exit`退出`tty2`，在图形界面应该可以打开`konsole`。之后的命令在`konsole`中执行。

安装文件管理器、浏览器
```Bash
pacman -S dolphin chromium
```

# 代理配置
`Arch Linux`的软件一般的在`pacman`仓库中，还有一些在`AUR`中。这里安装`yay`作为`AUR Helper`用来安装`AUR`中的软件。`yay`本身在`AUR`中，这里依照`github`上的[说明](https://github.com/Jguer/yay)手动安装
```Bash
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
```
用`yay`可以方便地安装`AUR`中的软件。之后软件的安装和更新都可以使用`yay`。

为了“翻墙”，需要`SSR`客户端。`SS`原本也可以，但是`代理服务商`只提供了`SSR`，这里就装`SSR`了
```Bash
yay -S shadowsocksrr-libev-git
```
这个会安装失败，原因是编译的时候把`warn`当作`error`来对待了。我fork了一份源码，修改了，暂时可以用这个
修改`PKGBUILD`，使源码指向fork的分支
```Bash
nano ~/.cache/yay/shadowsocksrr-libev-git/PKGBUILD
```
修改14行的源码地址为如下的分支
> 'shadowsocksrr-libev-git::git+https://github.com/whitecodes/shadowsocksr-libev.git'
删除已有的`git`信息和源码
```Bash
rm -rf ~/.cache/yay/shadowsocksrr-libev-git/shadowsocksrr-libev-git
rm -rf ~/.cache/yay/shadowsocksrr-libev-git/src
```
然后手动安装
```Bash
cd ~/.cache/yay/shadowsocksrr-libev-git
makepkg -si
```
这样应该可以顺利安装

根据`代理服务商`提供的配置编写配置文件，启动`SSR`
```Bash
ssrr-local -c ssrconfig.json
```
这样就在本地开启了`socket`代理服务，这里以端口号`1080`为例

启动后会占用一个输出窗口，新开一个窗口/标签用于输入接下来的命令

`SSR`本身只提供了`Socket`代理，更多的情况下，需要的是`http`代理。这里使用了`polipo`
安装`polipo`
```Bash
yay -S polipo
```
复制配置
```Bash
cp /etc/polipo/config .poliporc  
```
编辑配置
```Bash
nano ~/.poliporc
```
找到`parent SOCKS proxy`字段，修改为
> socksParentProxy = "localhost:1080"
> socksProxyType = socks5
我使用了`on-disk cache`，找到该字段，修改为
> diskCacheRoot = "~/.polipo-cache/"
建立文件夹
```Bash
cd ~
mkdir .polipo-cache
```
启动`polipo`
```Bash
polipo -c .poliporc
```

设置系统代理
打开`系统设置`->`网络`窗格下的`设置`->`代理`,选择`使用手动配置的代理服务器`，`HTTP 代理`填入本地`127.0.0.1`，端口填入默认的`8123`

打开`chromium`，可是应该可以访问`Google`了。在`chrome 应用商店`中安装`Proxy SwitchyOmega`，并设置代理。

日常使用的时候，`SSR`开机启动，`polipo`不启动，`chromium`由`Proxy SwitchyOmega`控制网络流量使用`SSR`开启的`socket`代理
打开`系统设置`->`开机和关机`->`自动启动`->`添加程序`，输入`ssrr-local`，点击确定。在属性页中选择`应用程序`，`命令`输入启动`SSR`的命令。应用退出。

# 软件安装
中文输入法用`fcitx`
```Bash
yay -S fcitx-im fcitx-cloudpinyin kcm-fcitx
```
默认全选就可以了
配置输入法
```Bash
nano ~/.xprofile
```
加入一下内容，保存退出
```
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS=@im=fcitx
```

这样重启后应该就可以输入中文了。
打开`Fcitx设置`->`附加组件设置`->点击`云拼音`的右边按钮来设置->`云拼音`选择`百度`

视频播放用`bomi`
```Bash
yay -S bomi-git
```
`bomi`安装会出错，先安装`bomi-git`版本

桌面环境用的`KDE`是基于`qt`的，所以日常用的软件也选用基于`qt`的。
压缩/解压工具用`ark`，图片浏览用`Nomacs`，dock 启动器用`Latte`，截图工具用`Spectacle`,下载用`aric2`，聊天用`telegram`，看手机通知用`kde connect`，图形化的编辑器用`vscode`
```Bash
yay -S ark nomacs latte-dock spectacle aria2 telegram-desktop kdeconnect visual-studio-code-bin 
```
`aria2`的用法以后再说