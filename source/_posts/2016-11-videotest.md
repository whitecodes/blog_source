---
title: 测试视频播放
date: 2016-11-02 19:31:13
tags:
- Life
---
继上次室友说要做视频网站之后，我也就试试可不可在这个网站上实现视频播放。
<!-- more -->
测试视频是`EVA`的`OP`。
<video src="http://oaigdk90m.bkt.clouddn.com/%5BOZC%5DNeon%20Genesis%20Evangelion%20Platinum%20Clean%20OP.mkv" controls="controls">
Your browser does not support the video tag.
</video>
简单来说就是直接写`HTML`标签，能不能正常播放就取决于浏览器的解码能力了。

比较尴尬的是这个博客是托管在`Github`上，但是视频源地址是`七牛`上。考虑要不要把这个博客搬回国内类似的平台。

更新：同样的一份博客也同时部署在国内的`Coding`上了。

尝试加入弹幕支持
```
{% dplayer "url=http://oaigdk90m.bkt.clouddn.com/%5BOZC%5DNeon%20Genesis%20Evangelion%20Platinum%20Clean%20OP.mkv" "api=https://api.prprpr.me/dplayer/" "id=2016-11-videotest" %}
```
更新：存储视频的`七牛`收回了域名，所有视频都播放不了了