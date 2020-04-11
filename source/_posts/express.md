---
title: express摸文档之路
date: 2020-03-28 17:20:20
tags: nodejs
---


**** 一、静态目录设置

1. nodejs中没有像apache等的提供静态文件夹，需要自己设置。
2. 静态文件夹也可以设置多个，只需要app.use()多个即可。

```
	const express = require('epxress')
	const app = express()
	app.use('/static',express.static(__dirname+'/public'))

	// '/static' 则localhose:3000/static访问到静态文件夹位置
	// 也可以不写，则默认访问根目录时，都能访问到静态文件夹位置
	// static(__dirname+'/public')) // '/public'为本地的需要作为静态文件夹的目录


```

<!-- more -->


*** 二、req属性

1. 当req是 *post* 请求时，获取参数需要用到插件`body-parser`
```
npm install body-parser

const bodyParser = require('body-parser')

...

app.use(bodyParser.urlencoded({extended: false}))
app.use(bodyParser.json())

app.post('/home', (req, res) => {
	console.log(req.body) // 获取到传回来的参数（json格式）
})

```

2. req.hostname 从请求头获取主机
3. req.query,当请求如'localhost:3000/user?id=9&name=xiaoming'时，可以通过req.query获取到参数，并且自动转为json格式
```
{
	id: 9,
	name: 'xiaoming'
}
```
4. req.params,当请求如'localhost:3000/user/9'时，可以通过req.params获取到参数，并且自动转为json格式。
也可以获取多个参数
```
// 单个参数时 localhost:3000/user/9
// 对应app.post('/user/:id')

{
	id: 9
}

// 多个参数时 localhost:3000/user/9/abc
// 对应app.post('/user/:id/:name')

{
	id: 9,
	name: 'abc'
}

```
5. req.path 获取请求URL

```
// example.com/users?sort=desc
console.log(req.path)
// => '/users'
```
6. req.route 获取当前路由的路由信息



*** 三、res属性

1. res.append, 可以用来设置响应头参数 `res.append(field [, value])`
`res.append('Set-Cookie', 'foo=bar; Path=/; HttpOnly')`

2. res.cookie, 设置响应头cookie参数 `res.cookie(name, value [, options])`
`res.cookie('name', 'tobi', { domain: '.example.com', path: '/admin', secure: true })`

3.res.json(obj),可以将数据以json格式返回，但也得传的是json格式

4. res.render(), 可以渲染一个文件到浏览器 `res.render(view [, locals] [, callback])`
5. res.download(file) 访问时，浏览器自动下载文件
6. res.type 设置mime类型
```
res.type('.html')
// => 'text/html'
```


*** 三、router

1. 通过`const router = express.Router()`来获取router，再通过router.get或router.post等方式设置路由路径。
它可以接收3个参数(req,res,next),其中，前2两个与上面一样，next可以用来处理一些其他状态，
但是需要注意的是，若参数中有加next，那么在最后必须加上next(),不然路由无法继续。
一般用来做中间件过滤器。
```
// index.js

router.get('/name', (req, res, next) => {
	console.log(req.path)
	res.send('user: '+req.params.id)
	// do things
	next()
})

```
2. 引用router时，通过app.use(require('/router/index.js'))
这时，访问localhost:3000/name时，路由会自定去找到index.js中的/name路径。

也可写成这样app.use('/user',require('/router/index.js'))
那么就需要访问localhost:3000/user/name才能定位到它了。