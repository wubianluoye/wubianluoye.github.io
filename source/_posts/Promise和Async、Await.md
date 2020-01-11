---
title: Pormise与Async/Await
date: 2020-01-11 10:10:00
tags: ES6
---


	#### SECTION 1  promise
	// 1.通过新建一个promise来进行异步，将等待上一个then执行完才会继续执行下一个then。执行时长为所有then的总和。(5000+2000 = 7000毫秒)
	// 2.若new Promise()返还的时reject时，直接执行catch

<!-- more -->

```

	let myPormise = new Promise((resolve,reject) => {
		let start = new Date().getTime()
		let obj = {
			result: 10,
			start: start
		}
		setTimeout(() => { resolve(obj) }, 5000)
		//reject('error') 错误只能在刚开始时返回？？？
	})

	myPormise.then((res,s) => {
		return new Promise((r,j) => {
			res.result = 20 + res.result
			setTimeout(() => { r(res) }, 2000)
		})
	}).then(res => {
		let end = new Date().getTime()
		console.log(res.result)
		console.log('1,times '+ (end - res.start) / 1000 + 's!')
	}).catch(err => {
		console.log('errortest', err)
	})

```

----


	#### SECTION 2  promise并发
	// 1.可以通过promise.all()方法来进行并发操作，用时则为当前最大延时(3000毫秒)

	```

	function f1(r) {
		return new Promise((res, rej) => {
			setTimeout(() => { res(r) }, 2000)
		})
	}

	function f2(r) {
		return new Promise((res, rej) => {
			setTimeout(() => { res(r) }, 1000)
		})
	}

	function f3(r) {
		return new Promise((res, rej) => {
			setTimeout(() => { res(r) }, 3000)
		})
	}

	function alldone() {
		let start = new Date().getTime()
		Promise.all([f1(100), f2(200), f3(300)]).then(res => {
			console.log(res.reduce((cur, pre) => cur + pre, 0))
			let end = new Date().getTime()
			console.log('2,done, times: '+ (end - start) / 1000+ 's')
		})
	}
	alldone()

```

----

	#### SECTION 3  async/await
	// 用时为(2000+4000 毫秒)

```

	function syncfun(r) {
		return new Promise((resolve, reject) => {
			setTimeout(() => { resolve(r) }, 2000)
		})
	}

	function syf(r) {
		return new Promise((res, rej) => {
			setTimeout(() => { res(r) }, 4000)
		})
	}

	async function resut () {
		try{
			let start = new Date().getTime()
			let a = await syncfun(100)
			let b = await syf(200)
			let end = new Date().getTime()
			console.log(a + b)
			console.log('3,use times: '+(end - start) / 1000 + ' seconds')
		} catch(e) {
			console.log(e)
		}
	}
	resut()

```

----




	#### SECTION 4  async/await+promise.all 并发
	// 用时为最大延时(4000毫秒)

```

	async function bresult() {
		let start = new Date().getTime()
		let [a, b] = await Promise.all([syncfun(100), syf(200)])
		let end = new Date().getTime()
		console.log(a + b)
		console.log('4,use times: '+(end - start) / 1000 + ' seconds')
	}

	bresult()

```

// promise 7.002s  // 依次
// pormise.all 3.004s  // 并发
// async/await  6.004s  // 依次
// async/await + promise.all  4.004s  // 并发

----

// 推荐文章：

[掘金：ES6系列文章 异步神器async-await](https://segmentfault.com/a/1190000011526612)