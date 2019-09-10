---
title: rebuild hexo
date: 2019-08-25 15:56:25
tags:
    - Code
    - Hexo
---
博客很久没有更新了，趁机尝试换了主题，然后重新新建了一遍
<!--more-->
# 初始化hexo
之前用的安装`hexo`使用的是`npm`，这次换了`yarn`。用新不用旧，和`npm`比就是下载的时候快一点。官方的实例是安装全局的`hexo-cli`，安装没有问题，但是安装完成后不能使用`hexo`命令。如果用`yarn run hexo`的方式来调用命令的话，就报没有`package.json`，不能使用。所以就创建一个文件夹来处理这个问题。

```bash
mkdir hexo
cd hexo
yarn add hexo
yarn hexo init blog
```

这样就初始化完成了，把原来的`_post`文件夹复制过来就基本完成了。剩下就是添加主题和更方便的一些脚本。

`hexo`的配置对照着原本的来就可以了，里面的选项都有说明，问题不大。

这样有两个`package.json`：外面的`hexo`下的和里面的`blog`下的。外面的只是用于一个`hexo`命令运行的环境，里面不会添加任何其他的依赖。里面的是`hexo`的相关配置，添加的插件都放在这里。剧里部署的时候需要安装[hexo-deployer-git](https://github.com/hexojs/hexo-deployer-git)

```bash
cd hexo/blog
yarn add hexo-deployer-git
```

# 安装主题
尝试找了各种主题，尝试下来好用的还是`NexT`。

为了看起来干净，删除`theme`。然后把主题下载到`theme`文件夹中。

```bash
cd hexo/blog/theme
git clone https://github.com/theme-next/hexo-theme-next.git
```

修改`hexo`的`_config.yml`来启用这个主题

``` yaml
# Extensions
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
theme: hexo-theme-next
```

原本是使用`npm`依赖来管理主题的，这里使用了`git`直接来下载。这样的话方便之后的升级。

# 配置主题
`NexT`更新了很多，这里就记一下新的功能。编辑主题的配置文件就可以了，路径是`hexo/blog/theme/hexo-theme-next/_config.yml`

## word count
这个用来显示字数统计，在配置中默认是启用的，但是需要下载依赖。

```bash
cd hexo/blog
yarn add hexo-symbols-count-time
```

这段的配置等下再更新上来。

## firestore
这个是用来记录阅读数量的。原本用的是`leancloud`，但是最近`leancloud`国内版要绑定域名才可以使用。这就牵扯到了域名注册备案一类的东西。
`firestore`是`google`家的数据库服务，看起来和`leancloud`差不多，具体内容可以看[官方的介绍](https://firebase.google.com/docs/firestore/)。

开启`firestore`的流程可以参考[Hexo NexT 主題的閱讀次數統計](https://blog.maple3142.net/2017/11/04/hexo-next-readcount/)
开启这个功能后，实际测试中会拖慢页面的加载，但是 who cares ？

## Progress bar

```bash
cd hexo/blog/theme/hexo-theme-next
git clone https://github.com/theme-next/theme-next-pace source/lib/pace
```

``` yaml
pace:
  enable: true
  theme: minimal
```
这个是页面加载的进度条。开启上面的`firestore`后就会长时间停留在99%。使用默认的样式不会影响阅读。

## background canvas
这个是背景的动画，有几种选择。原本用的是`canvas_nest`，页面有细小的线段会聚集在光标周围。挺好玩的，但是在手机浏览器端就卡卡的。

新看到的这个名字是`canvas_ribbon`。背景是随机折叠的彩带，鼠标点击一次就变换一次。

```bash
cd hexo/blog/theme/hexo-theme-next
git clone https://github.com/theme-next/theme-next-canvas-ribbon source/lib/canvas-ribbon
```

```yaml
canvas_ribbon:
  enable: true
  size: 500 # The width of the ribbon
  alpha: 0.27 # The transparency of the ribbon
  zIndex: -1 # The display level of the ribbon
```

透明度尽量调低一点，前景是白色，彩带透明度高的话会影响阅读。

# 脚本
事实上用`hexo`来写博客的话挺麻烦的，写作或者部署要好几个命令。原本是用[vscode-hexo](https://marketplace.visualstudio.com/items?itemName=codeyu.vscode-hexo)，直接使用`vscode`命令就可以了。但是这个扩展是基于`npm`的，暂时还没有看到`yarn`的扩展。

可以手动命令完成操作，基本命令就是`yarn hexo command`。会有警告说没有添加`License`。这里根本就不会以包的形式发布出去，所以在外面的`package.json`中加入`"private": true,`。

这里在最外面的`package.json`中加入预定义的脚本来简化操作：

默认命令是`hexo s`，然后打开浏览器。这里把这些操作连在一起`"dev": "xdg-open 'localhost:4000' && cd blog && hexo s"`。其中`xdg-open`是`linux`下的命令，`windows`是`start`，`Mac OS`是`open`。

部署之前最好是清空上一次的生成静态页面：`"deploy": "cd blog && hexo clean && hexo d -g && hexo clean"`。

部署的时候直接输入下面的命令就可以了
```bash
cd hexo
yarn deploy
```

上面的都修改完成后`package.json`是下面的样子

```json
{
  "name": "hexo-site",
  "version": "0.0.0",
  "private": true,
  "scripts": {
    "dev": "xdg-open 'localhost:4000' && cd blog && hexo s",
    "deploy": "cd blog && hexo clean && hexo d -g && hexo clean"
  },
  "dependencies": {
    "hexo": "^3.9.0"
  }
}
```

