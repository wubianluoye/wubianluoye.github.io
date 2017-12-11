---
title: webpack3.0躺坑css处理与es6编译
date: 2017-12-01 15:30:34
tags: webpack
---

Webpack3.0小案例躺坑css处理与ES6编译


css的编译与加载，基于style-loader、css-loader、postcss-loader、autoprefixer以及css预处理(以less为例)。
ES6的编译与加载，babel-preset-env代替babel-preset-2015。
<!-- more -->
css与js静态资源分割及注意事项，基于插件 extract-text-webpack-plugin。
css资源文件的编译与加载

css最基本的编译要依靠于style-loader、css-loader这两个加载器，所谓最基本，就是在不考虑使用css预处理器以及css后处理器的情况。预处理器包括less、scss、sass、stylus，后处理器如postcss的autoprefixer等。首先我们先弄明白各加载器的作用：

style-loader，将所有处理好的css样式以行内元素style标签的格式动态注入到界面的head标签中去。
css-loader，用来处理css样式，例如把css中类似@import()或者url()这样的引用资源进行引入或者处理。
autoprefixer，postcss提供用来自动处理css在不同浏览器之间前缀等问题，从这里我们也可以看出，使用autoprefixer需要先引入postcss。
预处理-loader，所有预处理器都是以css为终点生成文件的一种专门的编程语言，为css增加了一些编程特性。
了解了这些加载器各自的职能，那我们现在先小试牛刀，用代码来体验一下。

style-loader && css-loader

1、首先，我们在src项目文件夹下新建css文件夹，并新增一样式文件common.css，并写一些全局的css

html,
body {
    margin: 0;
    padding: 0;
    width: 100%;
    height: 100%;
    background: linear-gradient(to bottom, #abcdef, #f3f5f7);
}
2、安装css-loader和style-loader，进入到命令行，执行

cnpm i style-loader css-loader --save-dev
3、现在我们在入口文件app.js中去引入我们的common.css：

 //app.js
 import './css/common.css';
 ...
4、webpack配置，打开webpack.config.js,新增module配置项：

let path = require('path');
...
module.exports = {
    entry: path.resolve(__dirname, './src/main.js'),//入口文件地址
    output: {
        path: path.resolve(__dirname, './dist'),
        filename: 'js/[name]-[chunkhash].js'
    },
    module: { 
        rules: [ {
            test: /\.css$/,//匹配所有css文件
            use: [
                { loader: "style-loader" },
                { loader: "css-loader" }
             ]//指定加载器
            exclude: /node_modules///排除对node_module文件夹下面的所有资源的匹配
        }]
    },
    ...
}
需要注意，在指定加载器的时候，要注意各加载器的顺序，webpack中loader的解析是从右往左的顺序进行的，以当前为例，css文件首先是要通过css-loader进行处理，在将处理好的css交由style-loader，因此在指定加载器的时候，首先应该是style-loader,其次是css-loader。

现在，我们再次执行webpack编译命令，在浏览器中打开生成的index.html，发现页面背景已经如我们设置改变了,并且html文档中css也已经以style模式注入到head中了。

style-loader与css-loader加载
style-loader与css-loader加载
autoprefixer

上文我们已经说过，autoprefixer是css后处理器postcss提供的一个对css3中个别属性在不同浏览器下需要添加浏览器前缀的样式处理工具，因此在使用autoprefixer之前，我们需要安装postcss-loader来加载它。

 cnpm i postcss-loader autoprefixer --save-dev
1、现在，我们打开webpack.config.js配置文件，在module下配置autoprefixer

...
module: {
        rules: [{
            test: /\.css$/,
            use: [
                { loader: "style-loader" },
                { loader: "css-loader" },
                { loader: "postcss-loader" }//指定postcss加载器
            ],
            exclude: /node_modules/
        }]
    }
    ...
在配置postcss-loader之后，我们需要注意，由于css-loader处理文件导入的方式，因此加载器postcss-loader不能与CSS模块一起使用。 为了使它们正常工作，可以添加css-loader的importLoaders选项。。
...
module: {
        rules: [{
            test: /\.css$/,
            use: [
                { loader: "style-loader" },
                { loader: "css-loader",options: { importLoaders: 1 } },//importLoaders解决由于css-loader处理文件导入的方式导致postcss-loader不能正常使用的问题
                { loader: "postcss-loader" }//指定postcss加载器
            ],
            exclude: /node_modules/
        }]
    }
    ...
2、接下来，我们要给postcss-loader指定加载autoprefixer的操作，在根目录新建postcss.config.js

//postcss.config.js
module.exports = {
    plugins: [
        require("autoprefixer")()
    ]
}
在postcss.config.js配置文件中，我们给引入了autoprefixer,这样一来，在postcss-loader加载的时候，就会自动去读取该配置文件里面的配置项，加载autoprefixer了。

3、所有配置项已经完成，现在我们在原有的common.css文件中去添加样式，用来测试autoprefixer

html,
body {
    margin: 0;
    padding: 0;
    width: 100%;
    height: 100%;
    background: linear-gradient(to bottom, #abcdef, #f3f5f7);
}

.flexbox {
    display: flex;
    background: linear-gradient(to bottom, #abcdef, #096)
}
添加完成后，运行webpack命令进行编译，并在浏览器中打开index.html，打开控制台查看head标签下生成的style标签，已经可以看到autoprefixer已经自动为我们添加了浏览器前缀

autoprefixer
autoprefixer
autoprefixer 为我们提供了可以根据需求配置的参数，例如我么可以让它最终生成的css兼容最近的N个版本就好，但这不是我们本次讨论的重点，有兴趣的同学可以自己去官网查看。

//postcss.config.js  设置给最近5个版本的浏览器加前缀
module.exports = {
    plugins: [
        require("autoprefixer")({browsers:'last 5 version'})
    ]
}
autoprefixer-set-version
autoprefixer-set-version
css预处理（less-loader）

1、在src/css文件夹下新建style.less文件，并添加样式作为测试，并在入口文件app.js中引用该less文件

@base: #f938ab;

.box-shadow(@style, @c) when (iscolor(@c)) {
  -webkit-box-shadow: @style @c;
  box-shadow:         @style @c;
}
.box-shadow(@style, @alpha: 50%) when (isnumber(@alpha)) {
  .box-shadow(@style, rgba(0, 0, 0, @alpha));
}
.box {
  color: saturate(@base, 5%);
  border-color: lighten(@base, 30%);
  div { .box-shadow(0 0 5px, 30%) }
}
 //app.js
import './css/common.css';//引入css
import './css/style.less';//引入less
2、安装less以及less-loader，并在webpack配置项中进行配置。

cnpm i less less-loader --save-dev
在module配置项中新增一条对less文件处理的规则：

 ...
 module: {
        rules: [{
            test: /\.css$/,
            use: [
                { loader: "style-loader" },
                { loader: "css-loader", options: { importLoaders: 1 } },
                { loader: "postcss-loader" },
            ],
            exclude: /node_modules/
        }, {
            test: /\.less$/,
            use: [
                { loader: "style-loader" },
                { loader: "css-loader", options: { importLoaders: 1 } },
                { loader: "postcss-loader" },
                { loader: "less-loader" }//less放在最后，因为要最先加载（loader从右往左加载的规则）
            ]
        }]
    },
...
配置完成后，再次运行webpack命令，并在浏览器中查看效果，可以看到在head标签内又新增了一个style标签，并且已经将less编译成css了

less-loader
less-loader
Babel编译ES6

babel是一个js的转码器，将ES6或者ES7转换成ES5或者ES3；babel编译主要依靠于核心库babel-core，也就是说，用ES6进行编程而不需要担心浏览器环境是否支持。

babel-preset-env代替babel-preset-ES2015

在此之前，我猜测有很多的同学使用Babel的时候，preset必然会选择ES2015，但是最近babel官方推出了babel-preset-env，并建议在使用的时候选择env代替之前的ES20**。env为我们提供了更智能的编译选择，在此我们就不展开，有兴趣的同学可以去官网深入了解。

babel-preset-env编译ES6

1、首先，我们安装babel-loader、babel-core以及babel-preset-env，并在入口文件app.js中新增一段带ES6语法的js。

cnpm install --save-dev babel-loader babel-core babel-preset-env
import './css/common.css';
import './css/style.less';
//生成一个整数随机值，数值大于4则返回成功的Promise对象，否则返回错误的promise对象
function getData() {
    let promise = new Promise((resolve, reject) => {
        let key = ~~(Math.random() * 10);
        let temp = ['es6','babel']
        if (key >= 5) {
            let obj = {
                msg: "ok",
                data: [key,...temp]
            };
            resolve.call(this, obj);
        } else {
            let obj = {
                msg: "error",
                data: [key,...temp]
            };
            reject.call(this, obj);
        }
    })
    return promise
}
//找到页面中的Dom
let container = document.querySelector('#app');
//获取返回的结果并打印到界面
getData().then((data) => {
    container.innerHTML = JSON.stringify(data)
}, (err) => {
    container.innerHTML = JSON.stringify(err)
})
在这段代码中，我么使用了Promise来返回最后生成的结果，并将结果打印到界面中，其中运用了Promise、箭头函数、解构赋值等ES6的语法

2、现在，我们去webpack配置文件中新增一条规则：

...
  module: {
        rules: [{
            test: /\.css$/,
            use: [
                { loader: "style-loader" },
                { loader: "css-loader", options: { importLoaders: 1 } },
                { loader: "postcss-loader" }
            ],
            exclude: /node_modules/
        }, {
            test: /\.less$/,
            use: [
                { loader: "style-loader" },
                { loader: "css-loader", options: { importLoaders: 1 } },
                { loader: "postcss-loader" },
                { loader: "less-loader" }
            ]
        },
        //新增规则，编译js
        {
            test: /\.js$/,
            loader: 'babel-loader',
            exclude: /node_modules/
        }]
    },
并且在根目录下新增.babelrc文件，用来存放babel相关的配置：

//.babelrc
{
    "presets": [
        ["env",{
            "targets": {
                "chrome": 52,
                "browsers": ["last 2 versions", "safari 7"]
            }
        }]
    ]//设置编译场景，并配置目标结果兼容到chrome52版本以上等等。
}
3、然后我们去根目录下的index.html里面，新增一个div，并且给这个div的id取名app。

...
<body>
    <div id="app"></div>
</body>
...
现在我们在命令行运行webpack命令，并在浏览器中刷新界面，可以看到页面输出了我们结果。

babel编译es6
babel编译es6

并且，在生成的js中发现，通过编译后的js已经不再是es6的写法了,变异后的核心代码如下：
...
//编译后的js
function getData() {
    var _this = this;

    var promise = new Promise(function (resolve, reject) {
        var key = ~~(Math.random() * 10);
        var temp = ['es6', 'babel'];
        if (key >= 5) {
            var obj = {
                msg: "ok",
                data: [key].concat(temp)
            };
            resolve.call(_this, obj);
        } else {
            var _obj = {
                msg: "error",
                data: [key].concat(temp)
            };
            reject.call(_this, _obj);
        }
    });
    return promise;
}
var container = document.querySelector('#app');

getData().then(function (data) {
    container.innerHTML = JSON.stringify(data);
}, function (err) {
    container.innerHTML = JSON.stringify(err);
});
...
到此，通过babel编译js就基本完成，当然，babel提供了很多的插件和配置项，有兴趣的同学可以深入了解，本文暂不做深层次探讨。

CSS与JS文件分离

通过上面的操作，我们已经可以成功的将css和js进行编译打包，但是当我们再次去查看打包后的js文件时，发现js体积很大（目前js大小22kb），我们只是写了几行js而已啊，那么这又是怎么回事？

实际上，webpack在打包的时候，会把所有的模块，最终打包在一个文件里面，这也是为什么我们的js文件会如此之大，但却没看到有相应的css文件的原因。

静态资源处理器extract-text-webpack-plugin

extract-text-webpack-plugin用来实现不同文件的分离，其用法也和之前插件的用法一样。
1、安装插件

cnpm i extract-text-webpack-plugin --save-dev
2、在webpack配置项中对之前我们的css编译规则进行更改：

...
//引入插件
let extractTextPlugin = require('extract-text-webpack-plugin');
//初始化两个实例用于两处规则分别加载
let extractCSS = new extractTextPlugin('css/[name]-one.css');
let extractLESS = new extractTextPlugin('css/[name]-two.css');

 ...
  module: {
        rules: [{
            test: /\.css$/,
            exclude: /node_modules/,
            //extractCSS实例对css进行操作
            use: extractCSS.extract([
                // { loader: "style-loader" },//style-loader不能和插件一起使用
                { loader: "css-loader", options: { importLoaders: 1 } },
                { loader: "postcss-loader" }
            ])
        }, {
            test: /\.less$/,
            exclude: /node_modules/,
            //extractLESS实例对less进行操作
            use: extractLESS.extract([
                // { loader: "style-loader" },
                { loader: "css-loader", options: { importLoaders: 1 } },
                { loader: "postcss-loader" },
                { loader: "less-loader" }
            ])
        }, {
            test: /\.js$/,
            use: [{ loader: "babel-loader" }],
            exclude: /node_modules/
        }]
    },
    ...
     plugins: [
        //注册插件
        extractCSS,
        extractLESS
    ]
在配置中需要注意一下几点：

插件不能喝style-loader同时使用，原因是style-loader的作用是获取到js内部的样式并一行内形式插入到head标签中，但是extractTextPlugin插件是将js内部的代码提取成css文件并添加外部引用的方式进行加载。
在多处用到extractTextPlugin插件的时候，我们需要对插件进行功能性的拆分，就比如我们的项目中有两处规则用到它，因此我们首先对插件进行两次实例化得到两个实例，并分别用到两个处理规则中。
别忘了在plugins下进行注册。
最后，我们再看我们生成的js文件，已经只有4kb大小，减少了接近5倍的体积，可想而知，分离出css是很有必要的。

静态资源分离
静态资源分离