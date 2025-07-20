---
title: Samsung Galaxy Watch6
date: 2025-07-20 15:32:30
tag:
- Life
- App
---
买了二手的`Galaxy Watch6`，作为`Pokemon Sleep`的睡眠记录器。

<!---more--->

## Pokemon Sleep
最近在持续使用`Pokemon Sleep`，大概持续用了快一年了。记录睡眠来抽卡收复宝可梦的玩法很有趣，算是督促自己调整睡眠的动力。其中有点麻烦的是，这个游戏并不会自动记录睡眠，也不会从`google fit`之类的地方读取睡眠数据，更没有开放的接口。

官方的配件贵，并且只能连接游戏用，而且还是需要手动开启睡眠记录，仅仅只能是节约手机电池寿命而已。

之后看到了支持手表的[新闻](https://www.pokemonsleep.net/zh/news/313830373539363336333638393439323439/)

但是吧，支持的设备很少，只有三星、Google、苹果的部分设备。看数据流转过程Android侧是通过`Heath Connect`同步的。
然后就进行了第一次尝试：Zepp -> Heath Connect -> Pokemon Sleep
理论上睡眠数据是能够传到游戏中的，但是不行。猜测`Pokemon Sleep`认数据来源。
那没有办法了，等`Pokemon Sleep`更新支持更多设备。不奢求支持手上的`Amazfit T-Rex 2`，内心看好的是支持`Garmin`家的设备，到时候直接换了。

之后就没有然后了，没有更多的品牌支持，三星和谷歌的新机器有更新支持。那么就开始在这2个品牌中选择了。

谷歌本来是很好的选择，`Wear OS`官网支持。但是考虑的健康相关，谷歌的能力不看好：`Google fit`关闭网页版的仇还记着呢。新版的`Google fit`也不算好用。
三星没用过，想要试试。

## 购买
全新的三星手表很贵。抛弃“奢侈品”的功能，就功能来说，现在所有的智能手表都比较贵，特别是最新的版本的。就剩下的选择就是二手的上代旗舰版本。

开始频繁在闲鱼上搜索，大部分都是专业的贩子。
看到一个杭州面交的，于是愉快下单了。

这样，`Samsung Galaxy Watch6`就到手了。

## 连接
手机是国际版，已经root并且有代理可以访问互联网，有GAPPS。手表是国行的。所以一开始的配对就有问题。以下是解决的流程：

手机上下载[Galaxy Wear](https://play.google.com/store/apps/details?id=com.samsung.android.app.watchmanager)、[Samsung Health](https://play.google.com/store/apps/details?id=com.sec.android.app.shealth)、[Galaxy Watch6 Plugin](https://play.google.com/store/apps/details?id=com.samsung.wearable.watch6plugin)、[Wear OS 中国版](https://www.apkmirror.com/apk/google-inc/wear-os-by-google-china/)

另外还需要一个可以冻结系统应用的工具，冰箱、小黑屋之类的。

0. 对`Galaxy Wear`隐藏手机的root状态
    这个步骤可能不是必需的。我在连接的时候记得屏蔽了，但是现在去查看并没有屏蔽，功能也都正常。
1. 冻结`Google play 服务`和`Google play 商店`
    这个步骤是必需的，因为国行的三星手表宣传中只支持国行手机，所以会有检测步骤。
2. 手表开机，操作到等待配对界面
3. 开启`Galaxy Wear`，配对手表
    我在这里需要手动点击蓝牙配对通知，并且在手机是输入配对码，并且在手表上确认。
4. 在`Galaxy Wear`提示要安装应用的时候，解冻`Google play 服务`和`Google play 商店`
    我在这里直接将应用移除冻结列表了。这里需要先解冻`Google play 服务`。
5. `Galaxy Wear`会跳过应用的安装，然后卡在获取Google账户的阶段。这个时候硬等到超时，然后跳过就可以了。

后续的操作正常进行完成配对了。

## 同步到Pokemon Sleep
这里关键步骤是`Samsung Health`将睡眠数据同步到`Heath Connect`，国行又限制了。

0. 清空`Samsung Health`的数据
1. 开启`Samsung Health`的开发者选项和`Set features`菜单
    1.1. 用文件管理器创建 /sdcard/downloads/SamsungHealth/FeatureManagerOn
    1.2. 在`Samsung Health`的`关于三星健康`页面多次点击版本号，出现Set features选项。
2. 点`Developer mod(Samsung Health Data SDK)`，开启开发者模式
3. 点`Set features`，将Comnon下的CSC Country Code改成US。如果没有CSC Country Code选项，就先跳过，其他项设置后再回来设置。MCC Configuration改成310(US)。开启下面是个选项：
    Health Analytics-[HA] Server-DEV
    DataPlatform-Developer mode-on
4. 退出Set features选项。他会提醒你features已变更，请手动强行停止然后再启动程序，照做。
5. 如果前面没有设置CSC Country Code选项，现在完成设置。并且再次强行停止。
6. 再开三星健康，如果有提示登陆用户，那么登陆三星账户。这个是概率出现的。这里我用的外区申请的三星账户。在设置就能找到健康连接了，点开允许访问即可。
7. 如果登陆之后报错了，回到步骤0。换个时间再试。
8. 打开健康连接，如果看到三星健康有写入了，那么就成功了。

`Pokemon Sleep`侧的操作这里忽略了。

## 配置心电图和血压
三星的锁区很严重，心电图和电压都限制了使用区域。但是可以通过设置`Samsung Health`开发者选项来解锁。

这里主要起作用的是[SHM-MOD](https://github.com/ITDev93/SHM-MOD)。需要注意的是不要将这个开发者的应用语言切换成中文，机翻的，不如英文好理解。

参考这个[GPT翻译的教程](https://www.lxtend.com/miscs/SHM_MOD_Installation_and_Setup_Guide)进行操作。
这个`SHM MOD Companion`软件的操作逻辑和提示就算是英文也比较奇怪，建议直接看视频。主要迷惑的点是“6. 连接和侧载”，这里记录部分步骤供参考：

1. 在`SHM MOD Companion`下载手机版的`SHM MOD`并进行安装
2. 手表上开启无线调试后，`SHM MOD Companion`切换到`WATCH`页签。允许ADB,然后关闭各种提示弹窗。
3. 黑漆漆的终端不是用来输出的，是用来输出log的。点击`Watch SHM MOD`下载`手机版SHM MOD`，安装包会下载到 /sdcard/Downloads/SHM_MOD_Companion/watch 文件夹下。这里手机用的和手表用的用了不同的文件夹来区分。
4. 直接点击`connect`,会有弹窗供输入ip、配对码。
5. 连接上后点击`SELECT APK`选择前面下载的`手机版SHM MOD`，完成侧栽。

之后的操作步骤挺清晰的。

## 最终手段

以上都是建立在手表是国行系统的导致的限制，最终的解决方案就是手表刷机。在参考中列了教程和资源。注意教程中的手表型号是`SM-R930`。我手上的是`SM-R940`，所以应该是使用xda的固件。

## 参考
- [\[sleep\] Pokemon Sleep同步国行三星健康办法](https://bbs.nga.cn/read.php?tid=41818001&forder_by=postdatedesc)
- [在国内使用外版Galaxy Watch](https://www.lxtend.com/240215-initialize-the-US-version-galaxy-watch-in-china/)
- [原生Android适配使用国行三星Galaxy Watch 4](https://cloud-atlas.readthedocs.io/zh-cn/latest/android/hack/cn_samsung_galaxy_watch_4_wich_android.html)
- [非国行的Android不能使用国行的Watch4？不指望三星，自力更生完美解决](https://blog.xuegaogg.com/posts/samsung-watch4-china-mainland/)
- [外版 Galaxy 手机绑定国行 Galaxy Watch](https://blog.mitsea.com/49096fb476814f208319a80e768de2b7/)
- [国行 Galaxy Watch 6 刷美版固件](https://blog.mitsea.com/59cbf09805f54eb1b286a4b18e589313/)
- [Firmware SM-R940?](https://xdaforums.com/t/firmware-sm-r940.4650248/post-89695649)