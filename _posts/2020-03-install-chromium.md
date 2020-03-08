---
title: 安装 chromium
tags:
  - Code
date: 2020-03-08 15:12:35
---

周四那天晚上`github`和`steam`登不上了，记录下处理过程。
最后使用了`archlinuxcn`的`repo`中的`chromium-vaapi`

**没有具体的编译记录**
<!--more-->
## 无法访问 github
晚上9点多的时候发现无法通过`chromium`访问`steam`。

怀疑是被墙了，用`Proxy SwitchyOmega`切换到全局代理。还是不行。
`YouTube`可以访问，说明代理没有问题。怀疑是`steam`网页的问题。

`google`找找有没有碰到相同问题的，发现`github`也访问不了。

临时装了`FireFox`来测试，发现可以访问`github`和`steam`。

怀疑是`chromium`的问题。命令行启动，发现有错误输出
> ERROR:ssl_client_socket_impl.cc(941)] handshake failed; returned -1, SSL error code 1, net_error -100

对比可以访问的网站和不可以访问的网站，发现不可以访问的网站证书都是`DigiCert Inc`。按照这个思路尝试访问`Apple`官网，发现也不行。

遇事不决想要重新安装`chromium-vaapi`。发现`AUR`中的`chromium-vaapi-bin`包已经删掉了。

重装了`chromium`包，发现还是不行。

## 找到原因
第二天清空`chromium`配置，发现可以正常访问。从`Google`同步了数据之后，又不行了。

那就是同步的数据中造成了这个问题。开始排查

停用了`Proxy SwitchyOmega`后，就正常了。
那问题就是`Proxy SwitchyOmega`和`clash`冲突了。想了一下，之后就单纯使用`clash`了，反正已经有了网址过滤切换代理的功能

## 编译 chromium
为了查找问题，安装了没有硬件加速的`chromium`。`AUR`中的`chromium-vaapi-bin`包已经删掉了，只能手动编译`chromium-vaapi`

第一次编译，失败。
>fatal error: error in backend: IO failure on output stream: No space left on device

记起来之前为了编译速度配置了在内存中编译，修改配置，然后重新编译

编译的时候，有人说是
> 今天digicert的ocsp/crl ip被墙了

3个小时后，终于编译完成了。

**下面是走过的弯路**
然后开始编译必要的`libva-vdpau-driver-chromium`，看`AUR`[页面](https://aur.archlinux.org/packages/chromium-vaapi/)上的说明，我还需要手动修改脚本后再编译
> If you are having issues with NVidia, you need to build a patched libva-vdpau-driver-chromium

先下载包，但是不安装
``` sh
pacman libva-vdpau-driver-chromium
```
在询问`diff`的取消掉


然后修改下载回来的`PKGBUILD`
``` sh
cd ~/.cache/yay/libva-vdpau-driver-chromium
nano PKGBUILD
```

添加`Nvidia`的`patch`。这个可以在`chromium-vaapi`的[主题帖](https://bbs.archlinux.org/viewtopic.php?pid=1877774#p1877774)中找到，复制回来保存为`my.patch`
在`source`字段中添加`my.patch`，在`prepare()`字段添加下面的内容来启用
```
cd "${srcdir}"
patch -Np1 -i "${srcdir}/my.patch"
cd ../
```
其中最后切换工作目录的操作不确定是不是对的

手动编译并安装包
``` sh
makepkg -si --skipchecksums
```

之后才发现是不需要上面这些操作的

## 重新安装
`chromium-vaapi`的页面上有提到`archlinuxcn`上有预编译包
>Precompiled binaries of this wonderful package are available on archlinuxcn repository. Enjoy.

**最后的解决记录**

### 使用 archlinuxcn
可以直接参考`archlinuxcn`在`github`上的[使用教程](https://github.com/archlinuxcn/repo)，也可以使用下面的方法

``` sh
cat /etc/pacman.d/mirrorlist
nano /etc/pacman.d/mirrorlistcn
```
输入[中国源镜像列表](https://github.com/archlinuxcn/mirrorlist-repo)上的所有镜像，格式参考已有的`mirrorlist`。
然后修改`pacman`配置启用
``` sh
nano /etc/pacman.cnf
```
仿照其他源输入下面的内容
```
[archlinuxcn]
Include = /etc/pacman.d/mirrorlistcn
```

然后刷新`pacman`数据库，并导入密钥
``` sh
pacman -Syy
pacman -S archlinuxcn-keyring
```

### 安装对应包
``` sh
yay chromium-vaapi
yay libva-vdpau-driver-vp9
```
选择`archlinuxcn`的包进行安装

### 验证硬解播放
开一个视频网页，然后打开[chrome://media-internals/](chrome://media-internals/)，查看对应网页的`Player Properties`。查看`kVideoDecoderName`显示的内容，如果是`MojoVideoDecoder`或者`GpuVideoDecoder`就是开启了硬解。具体可以参考`chromium-vaapi`的[主题贴](https://bbs.archlinux.org/viewtopic.php?id=244031)