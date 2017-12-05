---
title: keep-alive使复用子组件不刷新
date: 2017-12-05 11:05:53
tags: Vue
---
问题：在写路由时，通过跳转到同一路由改变id来改变数据，发现只有第一次的数据有用，其他的点击还是第一个路由进来的数据。

原因：在<router-view></router-view>外面加了一层<keep-alive></keep-alive>

	<keep-alive>
		<router-view></router-view>
	</keep-alive>
<!-- more -->
keep-alive 缓存组件在内存中，再次进入该页面不会重新渲染，用于保存页面的原始状态。
这就是原因！！！

解决方法有几种：

1.删除keep-alive

2.保留keep-alive，使用$watch()进行检测

3.保留keep-alive，使用beforeRouteUpdate进行检测

4.保留keep-alive，使用beforeRouteEnter 进行检测

	  beforeRouteEnter (to, from, next) {
	    next(vm => {
	      vm.getdata()
	    })
	  }