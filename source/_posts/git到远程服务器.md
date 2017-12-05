---
title: git到远程服务器
date: 2017-12-05 10:53:48
tags: git
---
今天想把最近的一些小代码push到github上

我先是将该目录git init进行初始化，然后

	#git add -A 
<!-- more -->
接着

	#git commit -m “description...”
然后在github上新建一个远程库，想要将其与我本地的库相关联，使用：

	#git remote add origin git@github.com:YOURGITHUBNAME/REPOSITORY.git
并push到远程端

至此，一切顺利

于是，我使用了命令：

	#git push -u origin master
结果报错如下：

	error: failed to push some refs to 'git@github.com:你的远程库名.git'
	hint: Updates were rejected because the tip of your current branch is behind
	hint: its remote counterpart. Integrate the remote changes (e.g.
	hint: 'git pull ...') before pushing again.
	hint: See the 'Note about fast-forwards' in 'git push --help' for details.
从提示可以看出，是由于两者不同步，因此需要先pull，进行合并然后在进行push，
因此先使用

	git pull --rebase origin master
将远程文件同步下来。

然后再执行推送

	git push -u origin master
终于，成功！