---
title: 多设备同步hexo博客
date: 2017-11-30 17:25:47
tags: hexo
---
多设备同步hexo搭建的Github博客

多设备同步

同步思路与Github推拉源码思路相同，使用git指令，保持本地的博客文件与Github上的博客文件相同即可，其步骤如下：

    使用hexo搭建部署Github博客
<!--more-->
    // 在本地博客根目录下安装hexo
    npm install hexo
    // 初始化hexo
    npm init
    // 安装依赖
    npm install
    // 安装部署相关的配置
    npm install hexo-deployer-git

    详情参考[这里](http://www.jianshu.com/p/e99ed60390a8)

    上传博客工程
    上一步部署博客到Github以后，我们可以在Github仓库的master分支上看到我们上传的博客文件。

    Github主分支


    但是这个博客文件是不包含hexo配置的，所以我们需要新建分支，使用git指令将带hexo配置的Github工程文件上传到新建的分支上。

    Github上新建分支


    在本地博客根目录下使用git指令上传项目到Github:

    // git初始化
    git init
    // 添加仓库地址
    git remote add origin https://github.com/用户名/仓库名.git
    // 新建分支并切换到新建的分支
    git checkout -b 分支名
    // 添加所有本地文件到git
    git add .
    // git提交
    git commit -m ""
    // 文件推送到hexo分支
    git push origin hexo

    其他设备上clone下Github上新建的分支的文件到本地
    在另一台设备上使用git指令下载Github新建分支上的文件:

    // 克隆文件到本地
    git clone -b 分支名 https://github.com/用户名/仓库名.git

    本地写文章
    在source->_posts文件夹下新建md文件，并编辑好保存后：

    QQ截图20161013120208.png

    部署到Github

    // 安装hexo
    npm install hexo
    // 注意这里不需要hexo初始化：hexo init；否则之前的hexo配置参数会重置
    // 安装依赖库
    npm install
    // 安装部署相关配置
    npm install hexo-deployer-git

    同步项目源文件到Github

    // 添加源文件
    git add .
    // git提交
    git commit -m ""
    // 先拉原来Github分支上的源文件到本地，进行合并
    git pull origin 分支名
    // 比较解决前后版本冲突后，push源文件到Github的分支
    git push origin 分支名

至此多设备同步到此为止。
问题解决

由于公司里的电脑是win 10所以在部署博客的过程中会遇到一些问题，整理如下：

    Deployer not found: git
    在终端执行命令：

    npm install hexo-deployer-git --save

    然后继续执行hexo deploye指令进行部署。

    fatal: could not read Username for ‘ https://github.com ‘: Invalid argument
    由于使用的是https协议，安全性较高，所以系统终端不允许部署，所以我们该用 ssh，修改本地博客hexo配置文件_config.yml，将repository参数修改如下：

    repository: ssh://git@github.com/iNerdStack/inerdstack.github.io


    继续执行hexo deploye指令进行部署。

    Could not read from remote repository
    这是因为系统没有添加github的ssh信任到本机，所以我们要在命令行执行：

    ssh -T git@github.com
    yes

参考：
[【知乎】使用hexo，如果换了电脑怎么更新博客？](https://www.zhihu.com/question/21193762)
