---
title: 找工作
date: 2016-11-08 16:18:24
tags:
- Life
- Work
- Hexo
---
原本计划在10月底找到工作，但是...Too young.
<!-- more -->
## Hexo 更新

上周在`Hexo`上更新之后，就打不开了，各种静态资源的`404`。`Hexo`的开源主页上看到了相同的[issues](https://github.com/hexojs/hexo/issues/2235)。后来才发现出问题的文件是和主题相关的，在`Next`上找到了[解决办法](https://github.com/iissnan/hexo-theme-next/issues/1214)。问题的缘由是`Github Pages`忽略了`vender`文件夹。于是其中的那些文件都`404`了。

解决方法也简单，更新`Next`主题就可以了。但是自己定义的那些选项就需要重新再改一遍了。

为了测试是代码的问题还是`Github`的问题，我在`Coding`上又注册了一个账号用来测试。于是把`Hexo`配置成同时部署到两个托管服务。
```yml
deploy:
  ##github:
  - type: git
    repo: git@github.com:whitecodes/whitecodes.github.io.git

  ##coding:
  - type: git
    repo: git@git.coding.net:whitecode/whitecode.git,coding-pages
```
现在访问 [Github's White Blog](https://whitecodes.github.io) 和 [Coding's White Blog](http://whitecode.coding.me/) 效果是一样的。

## 原计划
原来的计划很美妙：9月底离职，10月底找到新工作，中间大约有一个月可以休息。然而……

## 现实
现在就一份 Offer ！