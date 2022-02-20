---
title: 使用Github Action部署Hexo
tags:
  - Code
  - Hexo
date: 2022-02-20 22:55:47
---
使用`Github Action`来部署`hexo`，这样电脑本地就不需要安装`npm`相关的东西了。另外利用`github.dev`也可以实现在页面上编辑了。

<!--more-->

## 创建Github Action文件

在`.github/workflow`目录中，创建一个名为`github-actions-demo.yml`的文件。

``` yaml
name: Hexo Github Actions
on: [push]
jobs:
    build:
      runs-on: ubuntu-latest
      steps:
        - name: Checkout source
          uses: actions/checkout@v1
        - name: Setup Node.js ${{ matrix.node_version }}
          uses: actions/setup-node@v1
          with:
            node-version: ${{ matrix.node_version }}
        - name: Cache NPM dependencies
          uses: actions/cache@v2
          with:
            path: ~/.npm
            key: ${{ runner.OS }}-npm-cache
            restore-keys: |
              ${{ runner.OS }}-npm-cache
        - name: Setup hexo
          env:
            ACTION_DEPLOY_KEY: ${{ secrets.HEXO_DEPLOY_PRI }}
          run: |
            mkdir -p ~/.ssh/
            echo "$ACTION_DEPLOY_KEY" > ~/.ssh/id_rsa
            chmod 600 ~/.ssh/id_rsa
            ssh-keyscan github.com >> ~/.ssh/known_hosts
            git config --global user.email "yourmailaddress"
            git config --global user.name "yourname"
            npm install hexo-cli -g
            npm install
        - name: Hexo deploy
          run: |
            npm run deploy
```

`Cache`不要抄`Hexo`网站上的，按照[Caching dependencies to speed up workflows](https://docs.github.com/cn/actions/using-workflows/caching-dependencies-to-speed-up-workflows)的来

> npm cache files are stored in `~/.npm` on Linux/macOS

这个文件提交到`Github`上应该就启动`Action`了，可以在`Actions`页上查看进度。

预计应该是部署失败的，因为部署的时候没有认证，这就需要添加证书

## 配置证书 

在本地电脑上生成证书，不要密码。
把`privatekey`内容复制到内容分支的`Actions secrets`中;

把`publickey`内容复制到发布分支的`Deploy keys`中。



## 在线编辑

`Github`有提供一个在线编辑的页面，在`Repo`页面按下按键`.`就可以打开编辑页面了，看起来是`Visual Studio Code Online`。