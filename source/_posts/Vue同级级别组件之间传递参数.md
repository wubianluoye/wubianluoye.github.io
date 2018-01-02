---
title: Vue同级级别组件之间传递参数
date: 2018-01-02 17:12:15
tags: vue
---
	<!DOCTYPE html>
	<html lang="en">
	<head>
	    <meta charset="UTF-8">
	    <title>Title</title>
	    <script src="js/vue.js"></script>
	</head>
<!-- more -->
	<body>
	<div id="app">
	        <my-comp></my-comp>
	        <my-comp2></my-comp2>
	</div>
	</body>
	<script>
	  Vue.component("myComp",{
	    template:"<div>我是组件1{{opser.name}}<button @click='send'>子组件之间传递参数</button></div>",
	    data:function () {
	      return {
	        opser : {id:1,name:'小明',password:"123456"}
	      }
	    },
	    methods:{
	      send:function () {
	        bus.$emit("myeven",this.opser); // $emit这个方法会触发一个事件
	      }
	    }
	  });
	  var bus = new Vue(); // 这个对象没有其他含义，仅仅就是一个信息对象，必须在2个组件都能读到的地方
	  Vue.component("myComp2",{
	    template:"<div>我是第二个组件--{{mynew.name}}</div>",
	    created:function () {
	      var that=this; // 这个要注意，this在此时指向信息传递对象，所以需要在外面重新指向this
	      bus.$on("myeven",function (msg) {
	        that.mynew=msg;
	      })
	    },
	    data:function () {
	      return{
	      	mynew:{ } // 先return定义一个空的
	      }
	    }
	  });
	  var app=new Vue({
	    el:"#app",
	  })
	</script>
	</html>