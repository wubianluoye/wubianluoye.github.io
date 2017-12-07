---
title: vue项目中的跨域
date: 2017-12-07 14:47:32
tags: [vue,跨域]
---
在写一些demo时，往往会借用别人的api，但是会有一个问题：有的api会有跨域问题，有的没有。那么，在有跨域问题时，我们使用
的axios是没法跨域的。所以...网上找了一波。

发现了可以使用jsonp这个轮子来用用。

<!-- more -->

    npm install jsonp --save && cnpm install jsonp --save

在需要的页面引入它就行了

	import jsonp from 'jsonp'

在axios的github中也有一个关于这个jsonp库的说明[文档](https://github.com/axios/axios/blob/master/COOKBOOK.md)

