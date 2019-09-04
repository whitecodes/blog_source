---
title: Zsh 安装和配置
tags:
  - Code
date: 2019-03-26 18:17:31
---

一直在使用`code::stat`来记录写东西的统计。`vscode`和`intellij idea`好处理，直接安装对应的插件就可以了。终端里面就比较麻烦，但是看到有`zsh`的[插件](https://gitlab.com/code-stats/code-stats-zsh)，感觉还是可以搞定的。这里就记录下安装`zsh`的记录
<!--more-->

# 安装 zgen

原本就在用`zsh`，但是`oh my Zsh`是手动配置的，安装比较麻烦，而且手动安装`code::stat`比较麻烦。看[网上的对比](https://www.reddit.com/r/zsh/comments/6pmr15/zplug_is_slow/)，在`zplug`和`zgen`中选了`zgen`。~~其实后来才发现大部分`shell`管理都是支持从网络上下载插件的~~

以下的记录都是以`zgen`为准，如果使用了其他的`shell`管理工具，请参考工具的主页。

下面的内容大部分是参考了`zgen`的[主页](https://github.com/tarjoilija/zgen)

在`Arch Linux`上安装比较简单，直接从`AUR`里安装就可以了。我这里用的是[yay](https://github.com/Jguer/yay)

``` shell
yay -S zgen
```

`zgen`需要依赖`zsh`，工具会自动安装。

# 配置 zsh

接下来进行`zsh`配置，使`zgen`生效。

1. 启动`zsh`。

第一次启动会有提示是否进行初始化流程，直接选择生成一个空的配置文件。

2. 编辑`.zshrc`

默认生成的配置文件是在`~/.zshrc`。里面应该是空的只有一条注释。添加下面的内容，使`zgen`生效。

``` shell
# load zgen
source "/usr/share/zsh/share/zgen.zsh"
```

上面的路径是安装路径/

3. 重启`zsh`

重启之后，输入`zgen`，如果没有出错就说明已经生效了。

# 配置 zgen

输入下面命令加载`oh my Zsh`基础，并初始化`zgen`。

``` shell
zgen oh-my-zsh
zgen save
```

根据[Code::Stats plugin for Zsh](https://gitlab.com/code-stats/code-stats-zsh)教程，这个插件读取环境变量中的`CODESTATS_API_KEY`来工作，在`.zshrc`中加入下面的语句来设置环境。

``` shell
# code::stat
export  CODESTATS_API_KEY="api key"
```

直接输入下面的命令就可以安装`code::stat`的插件了。

``` shell
zgen load https://gitlab.com/code-stats/code-stats-zsh.git
```

这样就已经可以生效了，但是更好的办法是把上面的命令加到配置文件中。

# 配置文件
这里备份下`.zshrc`的内容。

``` shell
# code::stat
export  CODESTATS_API_KEY="api key"

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
