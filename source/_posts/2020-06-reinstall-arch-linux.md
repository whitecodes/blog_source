---
title: 重新安装 Arch Linux
tags:
  - Code
date: 2020-06-21 22:29:38
---

折腾`Display manager`把系统搞坏掉了，重新安装。这里重新记录安装教程。之前的记录有一些已经变化不适用了。之后如果有变更也会继续更新这里。
<!--more-->
# 安装介质
使用一个U盘作为安装盘
从[Arch Linux Downloads](https://www.archlinux.org/download/)下载安装镜像。推荐使用`Magnet link`下载，也可以使用网页下方的中国镜像直接下载。

`Linux`环境有`dd`命令可以直接制作安装U盘。[详细说明](https://wiki.archlinux.org/index.php/USB_flash_installation_media#Using_dd)
```Bash
dd bs=4M if=/path/to/archlinux.iso of=/dev/sdx status=progress oflag=sync
```

`windows`环境也有对应的工具。印象里直接用`微软`官方的[Windows USB/DVD Download Tool](https://www.microsoft.com/en-us/download/windows-usb-dvd-download-tool)就可以制作安装U盘。具体的步骤可以参考[制作Win7安装U盘的过程](http://bbs.ngacn.cc/read.php?tid=4025346)

*注意事项：* 这个`安装盘`可以在日后的维护中当作`系统急救盘`使用，但是需要隔段时间就更新。

# XPS 的 UEFI 设置
根据`wiki`上关于`XPS 15 9560`的[相关说明](https://wiki.archlinux.org/index.php/Dell_XPS_15_9560#UEFI)，安装前需要修改一些`UEFI`设置。

# 安装环境配置 

插上U盘，重启。在出现`Dell`的图标的时候一直不停按`F12`，直到出现进度条。之后就进入`Boot Menu`，选择带`USB`字样的选项，进入安装环境。

屏幕是`4K`的分辨率，默认字体小。先改字体，目前只找到这个字体用的效果比较好
```Bash
setfont latarcyrheb-sun32
```

安装过程需要联网，使用 ~~`wifi-menu`~~ `iwctl`来连接网络。具体的用法参考[iwctl Usage](https://wiki.archlinux.org/title/Iwd#iwctl)
```Bash
# wifi-menu
iwctl
ping t.cn -c 4
```

安装的时候需要通过软件仓库安装软件。开启时间同步并使用国内的镜像。
```Bash
timedatectl set-ntp true
sed -i '/China/!{n;/Server/s/^/#/};t;n' /etc/pacman.d/mirrorlist
```
__上面的修改方法已经无效了，去[Pacman Mirrorlist Generator](https://archlinux.org/mirrorlist/)找一份最快的[镜像列表](https://archlinux.org/mirrorlist/?country=CN&protocol=http&protocol=https&ip_version=4&use_mirror_status=on)，手动输入下__

# 分区
用的是`EFI`，那就需要一个`EFI 系统分区`。`Arch Linux`本身需要一个分区分配给根目录`/`。以防内存不够用计划分`SWAP`分区备用。

计划的分区如下表

| 分区      |  大小  |  格式  |  路径  |         说明 |
|:----------|:------:|:------:|:------:|-------------:|
| nvme0n1p1 |  512M  | fat32  |   /EFI    | EFI 系统分区 |
| nvme0n1p2 |  8G   | [SWAP] | [SWAP] |     交换分区 |
| nvme0n1p3 | 160.5G |  btrfs  |   /    |       根分区 | 

使用`lsblk`确定SSD的磁盘名称。结果中带`nvme`字样的就是笔记本的SSD。其他以`loop`结尾的可以忽略。

然后使用`parted`或者`cfdisk`进行分区。安装的是`Arch Linx`单系统，直接删除原有的分区表，直接新建就可以了。

首先格式化`EFI 系统分区`，根据wiki上的[EFI 系统分区](https://wiki.archlinux.org/index.php/EFI_System_Partition)
```Bash
mkfs.fat -F32 /dev/nvme0n1p1
```

格式化交换分区
```Bash
mkswap /dev/nvme0n1p2
swapon /dev/nvme0n1p2
```
格式化根分区，用的`btrfs`
```Bash
kfs.btrfs -L xps /dev/nvme0n1p3
```

格式化完成后使用`lsblk`查看有没有错

先挂载分区到对应路径
```Bash
mount -o compress=lzo /dev/nvme0n1p3 /mnt
mkdir -p /mnt/boot
mount /dev/nvme0n1p1 /mnt/EFI
```

*注意事项：* 之后当作`系统急救盘`的来使用的时候也需要安装上面的来挂载。

# 安装系统
安装基础软件包组`base`和基础开发包组`base-devel`，后者用于安装`AUR`中的软件。`Linux`内核需要另外安装，已经排除在`base`组中了。
```Bash
pacstrap -i /mnt base linux linux-firmware base-devel
```
如果有选项默认回车就可以了。
如果之前修改软件仓库服务器用了国内的镜像，安装很快就会完成。

# 系统基础配置
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

__安装编辑器__
之后要编辑文件，这里先安装一个编辑器，简单的编辑用`nano`。安装系统后如果有复杂的编辑就直接使用`vscode`了。
```Bash
pacman -S nano
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

安装引导程序。这里直接使用传统的`GRUB`了
```Bash
pacman -S dosfstools grub efibootmgr
grub-install --target=x86_64-efi --efi-directory=/EFI --bootloader-id=grub
grub-mkconfig -o /boot/grub/grub.cfg
```

至此，`Arch Linux`可以算是安装完成了。根据官网的教程这个时候应该要重启了。但是并不能作为日常使用。接下来需要进行基本的配置和安装必要的软件，包括桌面环境

# 桌面环境
因为显示驱动的关系，开关机会挂起。
```Bash
nano /etc/default/grub
```
在`GRUB_CMDLINE_LINUX_DEFAULT`追加`acpi_rev_override=1`。加完之后该行差不多就是这个样子
```
GRUB_CMDLINE_LINUX_DEFAULT="quiet acpi_rev_override=1"
```

使用`xorg`作为图形界面的接口。这里偷懒直接全部安装了，反正存储不是特别紧张
```Bash
pacman -S xorg-server
```
不要直接安装`xorg`软件组，里面带有一些游戏……

不喜欢`gnome`，所以安装`KDE Plasma`
```Bash
pacman -S plasma-meta
```
查看`plasma-meta`中包含的[软件](https://www.archlinux.org/packages/extra/any/plasma-meta/)，里面用不到的可以删掉。

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
这里关机重启`XPS`可能会挂起，长时间无响应。长按电源键直接关机。然后按电源键开机进入新系统。

*注意事项*
安装用的U盘可以拔掉了

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
打开`系统设置`->`显示和监控`->`显示`，设置`缩放显示`到合适的大小，4K屏的`XPS`设置`200%`差不多。确定并应用。

然后重启，应该可以看到显示的字体可以看了，也可以在默认的右下角设置无线网络连接了。

# 安装软件

## 终端模拟器
`Arch Linux`安装软件一般都需要连接网络。先连接网络。
现在图形环境没有终端，先安装终端。

切换到`tty2`，登录

安装`konsole`
```Bash
pacman -S konsole
```

安装完后，`exit`退出`tty2`，在图形界面应该可以打开`konsole`。之后的命令在`konsole`中执行。

## 文件管理器、浏览器、编辑器
安装文件管理器、浏览器、编辑器。这里用`火狐`只是临时用用，之后最终使用的是带硬件加速的`chromium-vaapi`
```Bash
pacman -S dolphin firefox code
```

## 代理
代理使用的是`clash`
```Bash
pacman -S clash
```
下载代理的配置文件，这里放在`Clash.yaml`中。
运行代理
```Bash
clash -f /path/to/Clash.yaml
```
在`系统设置`->`网络 设置`->`代理`中选择`使用手动配置的代理服务器`。输入代理地址和端口，默认配置如下

|代理方式|代理地址|端口|
|:----|----|----:|
|HTTP 代理|127.0.0.1|7890|
|HTTPS 代理|127.0.0.1|7890|
|SOCKS 代理|127.0.0.1|7891|

代理默认使用的是第一个节点，切换节点打开[管理网页](http://yacd.haishan.me/)。如果不能打开就说明当前使用的代理节点挂掉了，先取消代理系统配置。默认的端口号是9090，默认的地址是127.0.0.1。
在[管理页面的proxies](http://yacd.haishan.me/#/proxies)中选择一个可用的代理节点就可以使用代理来访问了。
具体的有没有使用代理可以看[connections](http://yacd.haishan.me/#/connections)中的Chains部分

## git&AUR Helper
`Arch Linux`的软件一般的在`pacman`仓库中，还有一些在`AUR`中。这里安装`yay`作为`AUR Helper`用来安装`AUR`中的软件。`yay`本身在`AUR`中，这里依照`github`上的[说明](https://github.com/Jguer/yay)手动安装
```Bash
pacman -S git
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
```
用`yay`可以方便地安装`AUR`中的软件。之后软件的安装和更新都可以使用`yay`。

# 日常软件和配置

## zsh
默认的`bash`使用体验一般，安装`zsh`来加强体验，并且使用[code::stats](https://codestats.net/)插件。
`zsh`插件使用`zgen`来管理。直接安装`zgen`就可以，`zsh`作为依赖也会安装。
```Bash
yay zgen
```
安装后，输入`zsh`启动，选择创建一个空的配置文件。然后在`.zshrc`中加入下面的配置启用`zgen`。
```Bash
# load zgen
source "/usr/share/zsh/share/zgen.zsh"
```
退出`zsh`，重新打开`zsh`，输入`zgen`确认可以使用。退出`zsh`，再次修改`.zshrc`来启用`zsh`的插件。
```Bash
# Created by newuser for 5.8

# code::stat
export  CODESTATS_API_KEY="code::stats machine key"


# load zgen
source "/usr/share/zsh/share/zgen.zsh"


# if the init scipt doesn't exist
if ! zgen saved; then
    echo "Creating a zgen save"

    zgen oh-my-zsh

    # plugins
    zgen oh-my-zsh plugins/git
    zgen oh-my-zsh plugins/sudo
    zgen oh-my-zsh plugins/command-not-found
    zgen oh-my-zsh plugins/z
    zgen oh-my-zsh plugins/archlinux
    zgen load zsh-users/zsh-syntax-highlighting
    zgen load https://gitlab.com/code-stats/code-stats-zsh.git
    zgen load romkatv/powerlevel10k powerlevel10k

    # completions
    zgen load zsh-users/zsh-completions src

    # save all to init script
    zgen save
fi

# POWERLEVEL9K theme setting
POWERLEVEL9K_LEFT_PROMPT_ELEMENTS=(dir vcs)
POWERLEVEL9K_RIGHT_PROMPT_ELEMENTS=(status root_indicator background_jobs history time battery)
```
再次启动`zsh`可能会进入`powerlevel10k`的初始化流程，可以直接跳过。

日常的大部分操作是在`Konsole`中进行的。在`tty`中因为缺少部分字体，显示会有不完全的现象。只在`Konsole`中默认启用`zsh`。
`设置`->`编辑当前配置方案`->`常规`->`命令`中输入`zsh`的路径，默认是
> /bin/zsh

*注意事项：* `zsh`配置文件没有发挥`powerlevel10k`的全部功能，感觉还有改进的空间

## yay配置
这是用来安装软件的相关配置，包括了`pacman`、`makepkg`、`nano`的配置。

打开`pacman`的配置文件
> /etc/pacman.conf

要做的修改是确保`Color`选项启用。为了之后安装`steam`，开启`community`仓库。为了方便之后的`chromium-vaapi`，添加`Arch Linux CN`仓库，关于这个仓库查看[Arch Linux 中文社区仓库](https://www.archlinuxcn.org/archlinux-cn-repo-and-mirror/)项目主页。仓库镜像写在另一个文件中。
```conf
[archlinuxcn]
Include = /etc/pacman.d/mirrorlistcn
```
`mirrorlistcn`中内容是`Arch Linux CN`的镜像地址，格式仿照其他的`mirrorlist`。

使用前还需要更新并安装`archlinuxcn-keyring`。
```Bash
yay -Syy
yay -S archlinuxcn-keyring
```

`AUR`中的部分包需要下载源码后编译打包，在
机器内存足够而且多核CPU的情况下，修改`makepkg`配置经可能加快编译速度。
默认配置文件路径
> /etc/makepkg.conf 

主要修改是使用内存编译，并在压缩中使用多线程。
```conf
BUILDDIR=/tmp/makepkg
COMPRESSZST=(zstd -c -z -q - --threads=7)
```

`yay`默认使用`diff`的方式来显示`PKGBUILD`，修改为编辑的方式。更多的选项可以参考[项目主页](https://github.com/Jguer/yay)
```Bash
yay --editmenu --nodiffmenu --save
```

`nano`的修改不多，主要是代码高亮。本来想加上行号的，但是跨行复制的时候会把行号也复制出来。
默认配置文件路径
> /etc/nanorc

```Bash
## To include all existing syntax definitions, you can do:
include "/usr/share/nano/*.nanorc"
```
默认的配置中高亮的格式不多，这个[项目](https://github.com/scopatz/nanorc)提供更多的高亮格式。
```Bash
yay -S nano-syntax-highlighting
```

## chromium-vaapi
日常的电脑使用都是在浏览器中完成。其中大部分是看视频。其实使用软解也行，但是还是开启硬解。

`chromium-vaapi`是启用了硬件加速的`chrome`版本。安装浏览器本体很简单，在`archlinuxcn`镜像中有预编译版本。
```Bash
yay -S chromium-vaapi
```

麻烦的是安装`Nvidia`驱动并使用独立显卡。这里直接参考[网上的教程](https://blog.csdn.net/u014025444/article/details/91454059)

1. 安装闭源驱动
```Bash
pacman -S nvidia nvidia-utils nvidia-settings
```
2. 查看n卡的BusID
```Bash
lspci | egrep 'VGA|3D'
```

3. 自动生成配置文件
```Bash
nvidia-xconfig
```
这步我直接跳过了

4. 启动脚本配置

```Bash
$ nano /usr/share/sddm/scripts/Xsetup
======================================================================
xrandr --setprovideroutputsource modesetting NVIDIA-0
xrandr --auto
```
这是`SDDM`的配置，不同的窗口管理器设置不同

1. 修改配置文件

```Bash
$ nano /etc/X11/xorg.conf
======================================================================
Section "Module"                                                      #此部分可能没有，自行添加
    load "modesetting"
EndSection

Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BusID          "1:0:0"                                            #此处填刚刚查询到的BusID
    Option         "AllowEmptyInitialConfiguration"
EndSection
tails/91454059
```

1. 解决画面撕裂问题

```Bash
$ nano /etc/mkinitcpio.conf
======================================================================
MODULES=(nvidia nvidia_modeset nvidia_uvm nvidia_drm)
======================================================================

$ nano /etc/default/grub                                              # 此处必须是grub引导，其他引导自行百度
======================================================================
GRUB_CMDLINE_LINUX_DEFAULT="quiet nvidia-drm.modeset=1"               #此处加nvidia-drm.modeset=1参数
======================================================================

$ grub-mkconfig -o /boot/grub/grub.cfg                                # 就算grub引导，配置文件也可能不在一个地方，请查看清楚
```

1. `nvidia`升级时自动更新`initramfs`
```Bash
$ mkdir /etc/pacman.d/hooks
$ nano /etc/pacman.d/hooks/nvidia.hook
======================================================================
[Trigger]
Operation=Install
Operation=Upgrade
Operation=Remove
Type=Package
Target=nvidia
Target=linux
# Change the linux part above and in the Exec line if a different kernel is used

[Action]
Description=Update Nvidia module in initcpio
Depends=mkinitcpio
When=PostTransaction
NeedsTargets
Exec=/bin/sh -c 'while read -r trg; do case $trg in linux) exit 0; esac; done; /usr/bin/mkinitcpio -P'
```

内存够大，把浏览器的临时文件放到内存中。在`chromium`的启动命令后加上`%U --disk-cache-dir=/tmp/cache`

`chrome`使用的图形化是基于`gtk`的，文件选择的弹框安装`kdialog`

## steam
在[Proton](https://github.com/ValveSoftware/Proton)的加持下，用`Linux`玩游戏也不是不行。具体有哪些游戏支持在`Linux`下运行可以在[ProtonDB](https://www.protondb.com/)上查询。

安装`steam`需要启用32位源，打开`/etc/pacman.conf`，启用`multilib`

```Bash
yay -S steam
```
如果有问题，那么需要安装其他包，具体看[Wiki](https://wiki.archlinux.org/index.php/Steam/Troubleshooting)

## 常用软件

### 编辑器

`vscode`，使用官方的`code`包，或者去除微软信息的`vscodium`包

### IM

主要用的是`telegram`，使用`telegram-desktop-userfonts`包来使用系统字体。

### 手机同步

同步通知用的，`kdeconnect`

### 视频播放器

`bomi`    
目前还没有找到合适的`MPV`前端

### 文件管理器

`dolphin`

### 输入法

建议使用`fcitx`~~，还是使用旧的4版本的`fcitx`，`fcitx5`还是有点问题。另外还有安装`fcitx-qt5`来实现`qt`支持，安装`fcitx-cloudpinyin`来实现云拼音支持，安装`kcm-fcitx`来图形化配置工具~~
在用的是`fcitx5`，基本没问题，还有萌百的词库。

```
fcitx5
fcitx5-chinese-addons
fcitx5-configtool
fcitx5-gtk-git
fcitx5-material-color1
fcitx5-pinyin-moegirl
fcitx5-pinyin-zhwiki
fcitx5-qt
```

唯一的问题就是和浏览器`vivaldi`的搭配，这里使用`git`版本，暂时还没有出现问题。

### 截图工具

`Spectacle`

### 压缩工具

`ark`    
额外的格式支持

```
lrzip (optional) - LRZ format support
lzop (optional) - LZO format support
p7zip (optional) - 7Z format support
unarchiver (optional) - RAR format support
unrar (optional) - RAR decompression support
```

### 下载工具
`aria2`

### 远程工具
`freerdp`

# 系统美化和优化

## 字体
参考[Wiki](https://wiki.archlinux.org/index.php/Localization_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)/Simplified_Chinese_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#%E4%BF%AE%E6%AD%A3%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87%E6%98%BE%E7%A4%BA%E4%B8%BA%E5%BC%82%E4%BD%93%EF%BC%88%E6%97%A5%E6%96%87%EF%BC%89%E5%AD%97%E5%BD%A2)

## 主题
安装主题`breeze10-kde`

在`应用程序风格`-`窗口装饰`中选择`Breeze10` 。`Opacity`设置为 0% ，按钮大小设置为`很大`

`工作空间行为`-`桌面特效` `模糊` 设置合适的值

## 锁屏时间格式
参考`reddit`上的[这个帖子](https://www.reddit.com/r/Kubuntu/comments/8w79fc/i_want_to_lock_screen_to_display_time_in_24h/)

对于登录屏幕，修改`/usr/share/sddm/themes/breeze/components/Clock.qml`;对于锁屏，修改`/usr/share/plasma/look-and-feel/org.kde.breeze.desktop/contents/components/Clock.qml`

修改内容：將

``` qml
text: Qt.formatTime(timeSource.data["Local"]["DateTime"])
```

替换为

``` qml
text: Qt.formatTime(timeSource.data["Local"]["DateTime"],"hh:mm:ss")
```

## 输入法皮肤    
安装`fcitx-skin-material`

## 关闭Baloo索引 

``` shell
balooctl disable
``` 

## DOCK

`latte-dock`    
分隔使用`plasma5-applets-latte-spacer`
