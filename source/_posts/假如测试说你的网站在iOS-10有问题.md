---
title: 假如测试说你的网站在iOS 10有问题
date: 2018-02-08 11:43:28
tags: [webpack, safari, vue]
---
**这篇文章不那么有趣，只是解决了一个bug。但对我来讲，因为后面还要用Vue做很多项目，而且可以预见几乎每一个项目都会遇到这个问题，所以记录在案是有用的。**

一个bug

你用Vue做了一个单页面应用，它在一切设备上都工作正常，但是突然有一天，你的测试和你说，这个网站在iOS 10上跑不起来，怎么办？

于是你打开你电脑上的Chrome浏览器，工作正常；打开Safari浏览器，工作正常；打开iOS 11手机，工作正常；打开各种安卓手机，工作正常。但是在iOS 10的手机上，不论是微信浏览器，还是Safari浏览器，都只能看见一个白白的屏幕。

于是你把手机连上电脑，在电脑端的Safari里，看到了如下的错误：

SyntaxError: Cannot declare a let variable twice: 'e'.
可是你没有写过这样的代码，你怎么可能把一个名为e的变量定义两次？你打开代码，看到了这样美丽的代码：

	let e = e => {
	    console.log(e);
	    for (let e of [1, 2, 3])
	        console.log(e);
	};
虽然这段代码看上去比较奇怪，但是语法上有任何问题吗？哥就愿意定义一个名为e的函数，而这个函数的唯一入参名称也为e，并且哥的for循环体里还愿意再定义一个名称为e的变量，es6的变量作用域允许我们这样做，此e和彼e互不干扰，不对吗？况且很显然，这段代码不是人写的，而是我们在执行npm run build的时候编译产生的。

其实我们都没有错，我们也没写错，uglify也没搞错，错的是Safari本身。他们在第十七万一千零四十一号bug中承认了自己的错误：

	We incorrectly throw a syntax error when declaring a top level for-loop iteration variable the same as a parameter
当你定义一个与参数同名的for循环迭代变量时，我们错误地认为这是一个语法错误。
看，多么谦逊的态度。所以你也不用太纠结于一个白屏幕，只要找到解决方法就好了。方法其实很简单：

进入**build**文件夹；
找到**webpack.prod.conf.js**文件；
在**UglifyPlugin**的定义里添加关于**mangle的选项**，使它变成下面这个样子：

    new UglifyJsPlugin({
      uglifyOptions: {
        compress: {
          warnings: false
        },
        mangle: {
          safari10: true
        }
      },
      sourceMap: config.build.productionSourceMap,
      parallel: true
    }),

然后这个世界就太平了。

为了避免所有这些不必要的麻烦，我给vue-cli提的一个PR已被接受，在工具里缺省加入了这个选项，这样大家以后就不会遇到这个问题了。

关于这个bug的问题描述在这里，解决方案在这里，给vue-cli提的PR在这里，供深挖细掘的人参考。

另一个bug

实际上，除此之外，还有另外一个bug也会影响到vue网页在iOS 10上的展现，特别是当你用到广为流传的Swiper插件的时候。这是因为Swiper插件中用到了ES6的语法a = b ** c，a是b的c次方，而iOS 10的Safari里不认识这样的语法，认为这是一个错误，所以你需要让Swiper经过babel的包装，而缺省状态下babel是不对node_modules里的模块进行编译的。相关的issue见这里。

解决方法是在项目根目录下新建一个文件vue.config.js，在里面添加如下语句：

	module.exports = {
	  chainWebpack: config => {
	    config.rule('js').include.add(/node_modules\/(dom7|swiper)\/.*/)
	  }
	}

至止为止，通常情况下你的vue网页已经可以完美地在iOS 10上的Safari里展现了。

[原文链接](https://segmentfault.com/a/1190000013075464)