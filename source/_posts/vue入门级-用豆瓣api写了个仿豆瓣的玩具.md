---
title: 'vue入门级,用豆瓣api写了个仿豆瓣的玩具'
date: 2017-12-22 16:06:57
tags: vue
---
使用到的技术还是挺少的，只有vue+vue-router。<br>

github地址：[仿豆瓣-demo](https://github.com/wubianluoye/douban-test)
## 记录&问题： ##

<!-- more -->

在使用豆瓣api时，广播，小组，搜索都没找到api,so 后面3个都没写了

- vue-router
- 命名路由
- 由于豆瓣的api都得发起跨域请求，所以添加了**jsonp**这个库
- 首页添加了keep-alive组件做缓存
- 组件复用bookscomp.vue,moviescomp.vue
- mescroll.js库（本来打算做首页上拉加载的。。。）
- sass
- Eslint

---------------------------------------

**books.vue**<br>
通过组件传递参数，title和msg到bookscomp.vue子组件，使三个<books/>显示不同的内容，且不干扰。

	...

	<div class="books">
	  <books title="最受关注图书 | 虚构类" :msg="this.pdlist1"/>
	  <books title="最受关注图书 | 非虚构类" :msg="this.pdlist2"/>
	  <books title="最受关注图书 | 旅行" :msg="this.pdlist3"/>
	</div>

	...

**bookscomp.vue**<br>

	...

	<ul class="booksList">
	  <li class="" v-for="item in msg">
		<router-link :to="{ path: '/books/detail/' + item.id, params: {id:item.id} }" :key="item.id">
		  <img :src="item.image" alt="">
		  <p class="title">{{item.title}}</p>
		  <p v-if="">评分：{{item.rating.average === 0 ? '暂无评分' : item.rating.average}}</p>
		</router-link>
	  </li>
	</ul>

	...

子组件通过props:[]来接收数据，使用方法:直接在页面上使用（把它当作data中的数据使用！）

	export default {
	  props: ['msg', 'title'], // 子组件复用
	  components: {Loading},
	  data () {
	    return {
	      hot: [] // 没用到
	    }
	  }
	}

---------------------------------------

**更新**2017/12/22 14:37:05 

- 上拉加载更多完成！
- 在npm run bulid 之后，发现样式错乱了，然后把所有的文件都加上了scoped,就正常了。
- 使用了视图路由，通过该方法，实现了点击进入搜索页面后，导航条不显示。
- 对比了router.go（）和router.back()的区别。

**app.vue**

![](https://i.imgur.com/FFoAWas.png)


**router/index.js**

![](https://i.imgur.com/VgQ1844.png)

**nav.vue**

![](https://i.imgur.com/D7yG1UO.png)

**search.vue**

![](https://i.imgur.com/BWCxW3P.png)
