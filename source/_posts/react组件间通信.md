---
title: react组件间通信
date: 2017-12-05 11:39:20
tags: React
---

父组件可以给子组件传递属性，

父组件可以调用子组件的方法 ref，因为ref可以形成一条链，所以父辈组件可以调用到任何子孙组件的方法，其实可以直接更改状

态，不要这么做，会降低组件的内聚度，提高组件间的耦合度

<!-- more -->

子组件可以调用父组件的方法，前提是父组件已经把方法传递给子组件了

这样的话，只要外层有至少一个共同的父组件，这两个组件就都可以通过共同的父组件进行通信

	React.addons.LinkedStateMixin         为 react提供的 双向绑定的react-with-addons.min.js
	mixins:[React.addons.LinkedStateMixin]
	<input type="text" onClick={this.linkState('name')} style={{margin:"5px 0"}}/>
	
双向绑定。。
为单个数据挂载。。

	sexc:function(e){
		this.setState({
			sex:e.target.value
		})
	}


一个函数处理多个事件

	aal:function(type){
		var that=this;
				
		return function(e){
			var obj={};
					
			obj[type]=e.target.value;
			that.setState(obj);
		}
	}
	<div>
	<input type="text" onChange={this.aal('age')} style={{margin:"5px 0"}} />{this.state.age}
	<input type="text" onChange={this.aal('name')} style={{margin:"5px 0"}} />{this.state.age}
	<input type="text" onChange={this.aal('sex')} style={{margin:"5px 0"}} />{this.state.age}
	</div>