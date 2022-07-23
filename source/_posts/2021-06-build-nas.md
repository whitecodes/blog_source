---
title: 搭建NAS
tags:
  - Code
  - Life
date: 2021-06-06 21:51:43
---

买了矿渣`我家云`作为`NAS`。

<!---more--->
## 硬件设备

`我家云`是矿渣，买过来100+，最低价的时候50+

- CPU：rk3328 ARM Cortex-A53, 四核 1.5GHz
- GPU：2核ARM Mail-450
- 内存：1G
- Flash：8G
- 其他：内置usb to sata 3.5英寸硬盘位，1个千兆网口，1个USB 3.0口，1个USB 2.0口
- 电源：12V3A

## 系统

拿到手的时候，店家刷的应该是`Ride_Wind`的固件。带的导航页面太难看了，到手后还是刷了其他系统。

现在用的固件是从[这里](https://www.right.com.cn/forum/thread-1042032-1-1.html)来的。

1. 安装刷机工具
``` Bash
yay -S rkdeveloptool
```
2. 设备进入Maskrom模式
    1. 关机状态，我家云usb2.0口用双公头刷机线连接电脑
    2. 按住Reset键接通电源，不要放开reset，此时红灯先亮，
    3. 大约15秒后红灯熄灭，继续按住，直到AnroidTool发现Maskrom设备松开，刷机即可。
这个是网上的教程，参考下

如果机器之前没有刷过系统，那么进入的是`Loader`模式。这个时候往`EMMC`写入点东西，然后抹掉整个`EMMC`再重试上面就操作。

3. 刷入系统

参考这个[教程](https://roc-rk3328-cc.readthedocs.io/en/latest/flash_emmc.html)，结合其他教程的截图，依次执行下面的命令

``` Bash
rkdeveloptool db rk3328_loader.bin
rkdeveloptool wl 0x0 omv4-rk3328-0.7.9-armhf.img
rkdeveloptool rd
```

等待机器重启完成后，拔掉电源，插入网线接入路由器。

登陆路由器后台，查看机器的ip，浏览器登陆就可以看到主页了。

机器默认是DHCP，应该可以在路由器`DHCP Client list`之类的地方可以看到新增的`omv`设备。

默认的主页很丑，有修改的方法

## 配置

### 修改密码和CPU配置
修改系统`root`密码
```Bash
passwd root
```

修改`CPU`模式
```Bash
armbian-config
```
System->CPU->408000->1512000->interactive

各个CPU模式的说明
- interactive：交互模式，直接上最高频率，然后看CPU负荷慢慢降低，比较耗电。
- conservative：保守模式，类似于ondemand，但调整相对较缓，想省电就用他吧。Google官方内核，kang内核默认模式
- userspace：用户自定义模式，系统将变频策略的决策权交给了用户态应用程序，并提供了相应的接口供用户态应用程序调节CPU 运行频率使用。也就是长期以来都在用的那个模式。可以通过手动编辑配置文件进行配置
- powersave：省电模式，通常以最低频率运行。
- ondemand：系统默认的超频模式，按需调节，内核提供的功能，不是很强大，但有效实现了动态频率调节，平时以低速方式运行，当系统负载提高时候自动提高频率。以这种模式运行不会因为降频造成性能降低，同时也能节约电能和降低温度。一般官方内核默认的方式都是ondemand
- performance：性能模式！只有最高频率，从来不考虑消耗的电量，性能没得说，但是耗电量。


修改`SSH`欢迎信息
`/etc/update-motd.d/`
里面提示修改`/etc/default/armbian-motd`，但是更具体的修改还是修改这里的文件更方便


### 安装Fish

默认只有`Bash`，没有颜色提示，偷懒直接用`Fish`了
直接使用`apt`安装就可以了，如果出错就按提示修复依赖

### 修改主页&停掉不需要的服务

#### 主页

主页是`nginx`+`php`的，直接修改端口，看不到就没事了。
文件路径：`/opt/etc/nginx/vhost/entware.conf`

``` conf
server {
    listen 82;
    server_name localhost;
    root /opt/wwwroot/entware/;
    index index.html index.htm index.php;
    include /opt/etc/nginx/conf/php-fpm.conf;
    #otherconf
}
```
主页中自带了很多东西，用不到的都删除了。自带的东西在`/opt/wwwroot/entware`，这里记录下各个文件夹的内容

- cesu 测速
- douyinapi 抖音随机看小姐姐
- gl 爱特网站文件专家
- kod 可道云
- kuaishouapi 快手随机看小姐姐
- pi Pi 仪表盘
- pt nexusphp
- upjpg 图床
- uplz 蓝奏云上传
- web Transmission 网页
- veno Veno File Manager 3

还有一些应用在其他目录，通过`/opt/entware_init.sh`来启动，不要的全部注释掉

#### 关闭`Transmission`

因为我的用是`Aria2`。文件在`/opt/etc/init.d/S88transmission`

``` sh
#!/bin/sh

ENABLED=no
PROCS=transmission-daemon
ARGS="-g /opt/etc/transmission"
PREARGS=""
DESC=$PROCS
PATH=/opt/sbin:/opt/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

TRANSMISSION_WEB_HOME="/opt/share/transmission/web"

. /opt/etc/init.d/rc.func
```

#### 关闭Jellyfin

刷完系统后自带了`Jellyfin`，我期望是用`Docker`来使用应用。

```
systemctl stop jellyfin
systemctl disable jellyfin
```

### 风扇启停

固件中已经自带调速了，但是没有风扇启停，参考[恩山上的方案](https://www.right.com.cn/forum/thread-2025656-1-5.html)自己写了脚本。

``` Bash
#!/bin/sh
# gpio85为LED灯，gpio79为风扇,下文的max=70000和min=60000分别为风扇的起转温度和停转温度，70000=70摄氏度。监测刷新时间为20秒
if [ -n "$1" ]; then
    max=$1
else
    max=65000
fi
if [ -n "$2" ]; then
    min=$2
else
    min=55000
fi
echo $max, $min

while true; do
    t=$(/bin/cat /sys/class/thermal/thermal_zone0/temp)
    if [ "$t" -gt $max ]; then
       #  chmod 777 /opt/fan
       /opt/fan
      #  echo "CPU temp: $t > $max FAN POWER ON"
    fi
    if [ "$t" -lt $min ]; then
        killall fan > /dev/null 2>&1
        echo 79 > /sys/class/gpio/export
        echo "low" > /sys/class/gpio/gpio79/direction
        echo 79 > /sys/class/gpio/unexport
        # echo "CPU temp: $t < $min FAN POWER OFF"
    fi
    sleep 10
done
```

脚本保存为`/opt/pwnfan.sh`。接下来是设置脚本开机启动，新建文件`/opt/etc/init.d/S82pwnfan`

``` sh
#!/bin/sh

ENABLED=yes
PROCS=/opt/pwnfan.sh
ARGS=
PREARGS=""
DESC=$PROCS
PATH=/opt/sbin:/opt/bin:/opt/usr/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

. /opt/etc/init.d/rc.func
```

### SWAP设置
因为要用到图片的自动标签功能，需要使用机器学习，所以所有的1G内存就不够用了。系统自带了`zram`。但是最终还是要用内存，所以还是不够用，还是要手动在硬盘上设置`swap`。

```bash
# 创建SWAP文件并格式化
dd if=/dev/zero of=./swapfile bs=1M count=1536 status=progress
chmod 600 ./swapfile
mkswap ./swapfile

# 启用SWAP
swapon ./swapfile
```

## 文件分享

文件分享和备份主要用在局域网里面，直接用`NFS`了
通过`OMV`的页面可以直接操作，只是要注意下权限的问题

自动挂载没有成功过，现在手动挂载

`OMV`中自带了一个硬盘，通过页面删除不了，但是可以通过修改配置文件来删除，路径是`/etc/openmediavault/config.xml`。删除里面的硬盘和共享文件夹。

## 下载

下载使用的是`Aria2`，系统中自带了，这里记录下配置文件。之后如果`Aria2`如果有大版本更新，新增了功能应该会使用`Docker`版本。

``` ini
#允许rpc
enable-rpc=true
#允许所有来源, web界面跨域权限需要
rpc-allow-origin-all=true
#允许非外部访问
rpc-listen-all=true
#RPC端口, 仅当默认端口被占用时修改
#rpc-listen-port=6800

# token验证
rpc-secret=密码


#最大同时下载数(任务数), 路由建议值: 3
max-concurrent-downloads=5
#断点续传
continue=true
#同服务器连接数
max-connection-per-server=2
#最小文件分片大小, 下载线程数上限取决于能分出多少片, 对于小文件重要
min-split-size=10M
#单文件最大线程数, 路由建议值: 5
split=10
#下载速度限制
max-overall-download-limit=0
#单文件速度限制
max-download-limit=0
#上传速度限制
max-overall-upload-limit=75K
#单文件速度限制
max-upload-limit=0
#断开速度过慢的连接
#lowest-speed-limit=0


###进度保存相关
input-file=/opt/var/aria2/session.dat
save-session=/opt/var/aria2/session.dat
#定时保存会话，需要1.16.1之后的release版
save-session-interval=1800

log=/opt/var/aria2/log/aria2.log
log-level=error

#文件保存路径, 默认为当前启动位置
dir=/sharedfolders/exos/Download
#文件缓存, 使用内置的文件缓存, 如果你不相信Linux内核文件缓存和磁盘内置缓存时使用, 需要1.16及以上版本
#disk-cache=0
#另一种Linux文件缓存方式, 使用前确保您使用的内核支持此选项, 需要1.15及以上版本(?)
#enable-mmap=true
#文件预分配, 能有效降低文件碎片, 提高磁盘性能. 缺点是预分配时间较长
#所需时间 none < falloc ? trunc << prealloc, falloc和trunc需要文件系统和内核支持
file-allocation=falloc

###BT相关
#启用本地节点查找
bt-enable-lpd=true
enable-dht=true
enable-peer-exchange=true
#添加额外的tracker
bt-tracker=
#种子最大连接数
#bt-max-peers=55
#强制加密, 防迅雷必备
#bt-require-crypto=true
#当下载的文件是一个种子(以.torrent结尾)时, 自动下载BT
follow-torrent=true
#BT监听端口, 当端口屏蔽时使用
#listen-port=6881-6999
seed-time=60
seed-ratio=1
```
bt-tracker从[中国可用的 BT Tracker 服务器列表](https://www.yaozuopan.top/index.php/archives/1014/)获取，逗号分隔

## 照片

照片的处理就比较麻烦，期望本地版`Google Photo`带着改良的`UI`，但是现在也没有找到特别满意的。这里提供两个可用的：[PhotoPrism](https://photoprism.app/features)和[PiGallery2](https://bpatrik.github.io/pigallery2/)。两个都可以`Docker`部署，配置都在连接里有。

- `PiGallery2`可以显示人脸，但是所有的信息都需要手动写入`Exif`。
- `PhotoPrism`带一点点`AI`识别（需要大量内存），但是人脸部分还没有实现。新增照片后还需要建立索引。`Docker`需要用`photoprism-arm64`镜像。

## Jellyfin

没钱，所以用的还是`Jellyfin`。`Docker`部署没什么好说的，重点说一下需要装的插件。

~~- `Douban`用于抓取电影电视剧的豆瓣信息。其中电视剧需要按照季分开。但是这个插件已经很久不更新了，社区有个[替代方案](https://github.com/Libitum/jellyfin-plugin-douban/issues/45)。到不能用的时候再替换吧~~

~~- `AVDC`用来抓取日本电影的信息。[插件本体](https://github.com/xjasonlyu/jellyfin-plugin-avdc)需要搭配[远程工具](https://github.com/xjasonlyu/avdc-api/wiki/Docker%E5%AE%89%E8%A3%85%EF%BC%88%E6%8E%A8%E8%8D%90%EF%BC%89)部署在`VPS`上使用。~~

- `opendouban`用于抓取电影电视剧的豆瓣信息。其中电视剧需要按照季分开。[插件本体](https://github.com/caryyu/jellyfin-plugin-opendouban)需要搭配[远程工具](https://github.com/caryyu/douban-openapi-server)部署在`VPS`上使用。
- [javtube](https://javtube.github.io/README_ZH.html)用来抓取日本电影的信息。[插件本体](https://github.com/javtube/jellyfin-plugin-javtube)需要搭配[远程工具](https://github.com/javtube/javtube-server-releases)部署在`VPS`上使用，虽然官方推荐是部署在[Heroku](https://github.com/javtube/javtube-server-heroku)上


因为性能拉垮，`WEB`播放视频会比较吃力，上PC上有客户端内置了`MPV`，手机上使用`Jellyfin`客户端可以调用`MPV`播放。

听音乐的话，手机上另外有[Gelli](https://github.com/dkanada/gelli)

[其他平台](https://jellyfin.org/docs/general/clients/index.html)的话基本上也有对应的客户端

但是现在局域网内还是挂载之后本地看，在外面的话就需要内网穿透

## FRP

内网穿透用的是`frp`的方案，主要是需要`VPS`部署`Server`端，本地运行`Client`端，都可以使用`Docker`部署

- Server frps.ini
```ini
[common]
bind_port = 7000
dashboard_port = 7500
dashboard_user = admin
dashboard_pwd = pwd
auto_token = auth
```
其中`dashboard_pwd`用于`dashboard`登陆，`auto_token`用于`Server`和`Client`之间通信

- Client fprc.ini

``` ini
[common]
server_addr = 
server_port = 7000
auto_token = auth

[aria]
type = tcp
local_ip = 127.0.0.1
local_port = 8081
remote_port = 8081
```
因为是用来对外提供web服务，`type`只需要`tcp`。
