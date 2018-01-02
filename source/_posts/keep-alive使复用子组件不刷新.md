---
title: keep-alive使复用子组件不刷新
date: 2017-12-05 11:05:53
tags: vue
---
问题：在写路由时，通过跳转到同一路由改变id来改变数据，发现只有第一次的数据有用，其他的点击还是第一个路由进来的数据。

	<keep-alive>
		<router-view></router-view>
	</keep-alive>
<!-- more -->

vue2.0提供了一个keep-alive组件用来缓存组件,避免多次加载相应的组件,减少性能消耗

keep-alive 缓存组件在内存中，再次进入该页面不会重新渲染，用于保存页面的原始状态。

vue2.0 keep-alive最佳实践：

	<keep-alive>
	<component>
	  <!-- 组件将被缓存 -->
	</component>
	</keep-alive>

有时候 可能需要缓存整个站点的所有页面,而页面一般一进去都要触发请求的
在使用keep-alive的情况下

	<keep-alive><router-view></router-view></keep-alive>

将首次触发请求写在created钩子函数中,就能实现缓存,
比如列表页,去了详情页 回来,还是在原来的页面

2.缓存部分页面或者组件

(1)使用router.meta属性

	// 这是目前用的比较多的方式
	<keep-alive>
	    <router-view v-if="$route.meta.keepAlive"></router-view>
	</keep-alive>
	<router-view v-if="!$route.meta.keepAlive"></router-view>

router设置

	... 
	routes: [
		{ path: '/', redirect: '/index',  component: Index, mate: { keepAlive: true }},
		{
		  path: '/common',
		  component: TestParent,
		  children: [
		    { path: '/test2', component: Test2, mate: { keepAlive: true } } 
	  ]
	}
	....
	// 表示index和test2都使用keep-alive

(2).使用新增属性inlcude/exclude

2.1.0后提供了include/exclude两个属性 可以针对性缓存相应的组件

	<!-- comma-delimited string -->
	<keep-alive include="a,b">
	  <component :is="view"></component>
	</keep-alive>
	<!-- regex (use v-bind) -->
	<keep-alive :include="/a|b/">
	  <component :is="view"></component>
	</keep-alive>
	
	//其中a,b是组件的name

注意:这种方法都是预先知道组件的名称的

(2)动态判断
	
	<keep-alive :include="includedComponents">
	  <router-view></router-view>
	</keep-alive>

includedComponents动态设置即可

原文链接：  [vue2.0 keep-alive最佳实践](http://blog.csdn.net/qq_32786873/article/details/71173576) 