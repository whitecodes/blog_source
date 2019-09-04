---
title: build by hexo
date: 2016-07-09 16:35:51
tags:
- Code
- Hexo
description: 使用Github和Hexo建立自己的Blog
---
# HELLO WORLD

受`Tony`影响，一时兴起就开始建这个博客。把过程写下来也算是留个记录。

## 环境和工具
平台是`winows 7 X64`，同时要保证可以顺利访问`Github`。都不能上`Github`怎么来建站？当然其他代码托管网址应该也是可以的（猜测）。我这里使用`Shadowsocks`来访问国际网络。~~这里有一个坑。~~
命令行工具用的是`Cmder`。原因嘛……颜值比较高。~~这里是另一个坑。~~ 使用其他工具也是可以的，但是不建议使用`windows`自带的`cmd`工具。如果平台是`windows 10`的话，可以试试自带的`Bash`。
`Cmder`是一个开箱即用的工具。在[其官网](http://cmder.net/ "Portable console emulator for Windows")下载回来后打开就可以使用了。
![Cmder 界面](http://cmder.net/img/main.jpg)
注意：为了以后的方便，请务必下载完整包

## 安装 Hexo
`Hexo`的[官网](https://hexo.io/zh-cn/ "Hexo")上提供的安装方式是`npm`，所以需要先安装`node.js`。
这里推荐使用[chocolatey](https://chocolatey.org/ "The package manager for Windows")来安装。自己手动安装的话，还需要配置环境变量。

### 安装 chocolatey
以管理员权限打开`Cmder`。输入命令
``` bash
λ @powershell -NoProfile -ExecutionPolicy Bypass -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
```
这里全部使用`chocolatey`的默认设置，如果需要自定义的话，访问[Installing Chocolatey](https://chocolatey.org/install#before-you-install "Installing Chocolatey")。

### 安装 node.js
作为一个`windows`下的包管理器，`chocolatey`以命令行安装软件。安装`node.js`，需要在`Cmder`中输入
``` bash
λ choco install nodejs
```
然后耐心等待完成。
`npm`作为`node.js`的默认包管理工具，安装完成后就可以直接使用了。

## 安装 Hexo
对照`Hexo`网站的教程，就剩下最后一步了
``` bash
λ npm install -g hexo-cli
```
没有出错的话，`Hexo`的安装就已经完成了。

## 建立 GitHub Pages
`Github Pages`是一项不限流量的静态页面托管服务，本身是为了展示项目和开发者用的。现在更多的是当作小型`Blog`来使用。

### 申请 Github 账号
就像其他网站申请账号一样，能不填就可以不填

### 链接 Github
默认使用`SSH`链接，为了安全需要验证`SSH Key`。这段参考[网上教程](http://www.jianshu.com/p/05289a4bc8b2 "如何搭建一个独立博客——简明Github Pages与Hexo教程")的`SSH`部分

#### 检查SSH keys的设置
``` bash
$ cd ~/. ssh 检查本机的ssh密钥
```
如果提示：`No such file or directory` 说明你是第一次使用`SSH`

#### 生成新的SSH Key
``` bash
$ ssh-keygen -t rsa -C "邮件地址@youremail.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/your_user_directory/.ssh/id_rsa):<回车就好>
```
然后系统会要你输入密码：
``` bash
Enter passphrase (empty for no passphrase):<输入加密串>
Enter same passphrase again:<再次输入加密串>
```
在回车中会提示你输入一个密码，这个密码会在你提交项目时使用，如果为空的话提交项目时则不用输入。这个设置是防止别人往你的项目里提交内容。
注意：输入密码的时候没有`*`字样的，你直接输入就可以了。
最后看到这样的界面，就成功了
![SSH](http://cnfeat.qiniudn.com/11.png)
~~请原谅我的盗图~~

#### 添加SSH Key到GitHub
在本机设置SSH Key之后，需要添加到GitHub上，以完成SSH链接的设置。

1. 打开本地`id_rsa.pub`文件。可以通过`Everything`或`Listary`输入文件名直接找到文件。打开的工具系统自带的`记事本`就可以了。或者将密匙复制到剪贴板中
``` bash
λ clip < ~/.ssh/id_rsa.pub
```
2. 登陆`Github`。点击右上角的头像：`Setting`->`SSH and GPG keys`->`New SSH key`
3. 输入`Title`，将密匙复制到`Key`输入框

#### 测试 SSH
可以输入下面的命令，看看设置是否成功，`git@github.com`部分不要修改
``` bash
$ ssh -T git@github.com
```
如果是类似下面的反馈：
``` bash
The authenticity of host 'github.com (207.97.227.239)' can't be established.
RSA key fingerprint is 16:27:ac:a5:76:28:2d:36:63:1b:56:4d:eb:df:a6:48.
Are you sure you want to continue connecting (yes/no)?
```
输入`yes`就好
``` bash
Hi yourname! You've successfully authenticated, but GitHub does not provide shell access.
```
`yourname`部分是你的`Github`账号就说明链接成功。

#### 一点小事
`Git`会根据用户的名字和邮箱来记录提交。`GitHub`也是用这些信息来做权限的处理，输入下面的代码进行个人信息的设置，把名称和邮箱替换成自己的
``` bash
$ git config --global user.name "yourname"//用户名
$ git config --global user.email  "邮件地址@youremail.com"//填写自己的邮箱
```

### 建立 Github Pages
这里基本可以跟着[官方教程](https://pages.github.com/ "Websites for you and your projects.")走。
建立名称为`username.github.io`的`repository`，其中`username`用你的账户名替换。
在该`repository`的`Setting`标签页下找到`GitHub Pages`，点击`Launch automatic page generator`。接着要你确认一些名称啥的。不用管，点击网页下方的`Continue to layouts`。然后是选择主题，随便选一个看得顺眼的，点击`Publish page`。完成！
到此为止，专属的`Blog`已经可以算建立了。浏览器访问`username.github.io`(`username`是账户名)已经可以看到一个网页了。

## 建站
总体的思路是：使用`Hexo`将本地资源生成静态站点，上传到`Github`。也就是说：前面的`Github Pages`名称主题什么的全部都会被替换成本地生成的。这里跟着`Hexo`的[官方教程](https://hexo.io/zh-cn/docs/setup.html)走。
安装 Hexo 完成后，请执行下列命令，Hexo 将会在指定文件夹中新建所需要的文件。
``` bash
$ hexo init <folder>
$ cd <folder>
$ npm install
```
第一条命令就碰到一开始提到的坑了：`npm`依赖下载不了……就算`Shadowsocks`开启全局，也不能拯救。
处理方法是使用国内镜像。在执行以上命令之前，先执行：
``` bash
npm config set registry http://registry.cnpmjs.org
```
或者
``` bash
npm --registry http://registry.cnpmjs.org info express
```
两者都是临时的。
现在再执行上面的就不会卡网络了。

``` bash
$ hexo generate #生成站点
$ hexo server #启动服务器
```
浏览器打开`http://localhost:4000`就可以看到默认的`Hexo Blog`了。

## 部署
本地已经有了一个网站，需要把它放到网上。
打开`Hexo`站点目录下的`_config.yml`。找到最后的`deploy`部分
``` bash
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: git@github.com:yourname/yourname.github.io.git
```
其中`yournam`替换成`Github`账号。
到最后激动人心的上传部分了。
``` bash
$ hexo deploy
```
结果是：失败！这里就是开头提到的“另一个坑”：`Cmder`默认不启用`ssh-agent`。
解决办法是在`Cmder`的`init.bat`文件中加上这么一段
``` bash
@echo off
ssh-agent | grep -v echo | sed -e "s/^/@set /" | sed -e "s/;.*$//" - > call.cmd
call call.cmd
del call.cmd
ssh-add "%HOME%\.ssh\id_rsa"
@echo on
```
重新打开`Cmder`，执行
``` bash
$ hexo deploy
```
没有报错的话就可以在`yourname.github.io`访问自己的`Blog`了。
