---
title: vue项目在同一局域网无法打开解决方法
date: 2017-12-11 16:35:47
tags: [Vue,webpack]
---
**问题：**
	
  1. 在使用vue-cli生成的vue文件想在手机上查看时，发现无法打开。（已将locahost：8080/替换成本机地址192.168.190.225：8080）
  2. 已将node.js从防火墙中打勾。
问题还是一样，手机上无法打开。

<!-- more -->

**解决方案：**

  **找到vue项目的package.json文件**，

**修改前**

	"scripts": {
	    "dev": "webpack-dev-server --inline --progress --config build/webpack.dev.conf.js",
	    "start": "npm run dev",
	    "unit": "jest --config test/unit/jest.conf.js --coverage",
	    "e2e": "node test/e2e/runner.js",
	    "test": "npm run unit && npm run e2e",
	    "lint": "eslint --ext .js,.vue src test/unit/specs test/e2e/specs",
	    "build": "node build/build.js"
	  },

在"dev": "webpack-dev-server --inline --progress --config build/webpack.dev.conf.js"

中添加 --host 192.168.190.225（注192.168.190.225改为你的本机地址），保存即可。

**修改后**

	"scripts": {
	    "dev": "webpack-dev-server --inline --progress --config build/webpack.dev.conf.js --host 192.168.190.225",
	    "start": "npm run dev",
	    "unit": "jest --config test/unit/jest.conf.js --coverage",
	    "e2e": "node test/e2e/runner.js",
	    "test": "npm run unit && npm run e2e",
	    "lint": "eslint --ext .js,.vue src test/unit/specs test/e2e/specs",
	    "build": "node build/build.js"
	  },