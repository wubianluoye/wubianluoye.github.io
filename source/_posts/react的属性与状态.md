---
title: react的属性与状态
date: 2017-12-05 11:44:14
tags: React
---

`this.props` 表示那些一旦定义，就不再改变的特性，而 `this.state` 是会随着用户互动而产生变化的特性。

属性 props

`this.props`获取属性

<!-- more -->

状态 state

`this.stste`获取状态

属性是在一个组件被声明，尚未初始化的时候就被父组件赋予的，并且不可以自己修改

在react中，state属于组件的自有属性
 
getInitialState初始化每个实例特有的状态
 
`setState({name:"red"})`来更新组件状态 
 
每次状态更新之后，react会对组件进行一个diff的算法，查看一下是否需要更新dom   如果需要更新dom，就会重新执行render方法

react数据传递使用两种事物进行传递：

1.属性（props）     在组件外部传入，或者内部设置，组件内部通过this.props获得

2.状态（state）     在组件内部设置或者更改，组件内部通过this.state获得
 
属性是在一个组件被声明，尚未初始化的时候就被父组件赋予的，并且不可以自己修改，就像咱们的姓氏是爸爸赋予的，爸爸可以改，但是你自己不能改
 
属性就是为了使用数据

如何让一个组件有属性 3种
 
1.在渲染的时候给它传递 2.父组件给子组件传入  3.组件自己初始化属性
 
属性被传递的方式一般是在组件被调用的时候被传入
 
 
可以通过在ReactDOM.render的时候去给组件传入一个提前设置好的数据，键值对方式
 
	var d='my component';
	var e={name:"allen",age:18};
	var f=[0,1,2]
	ReactDOM.render(<App title={d} info={e} arr={f}/>,document.getElementById('app'))
 
获取的时候也很简单，创建组件里有一个东东叫`this.props`
 
里面保存了所有传入的数据，使用时记得加{}  不加的话，react不认识
 
还有一种情况就是三个点   ...

	 var props={
	     name:'allen',
	     age:18
	}
	<Hello {...props}  sex={'male'}/> 
 
`this.props` 就是这样的`{name:'allen',age:18,sex:'male'} `
 
 
这是根据别人给传的，那么怎么能自己初始的时候就有呢，当然可以
 
	getDefaultProps:function(){
	     return {
	          name:'tom'
	     }
	},
	render:function(){}
 
 
这个时候的数据也就会保存在this.props里，那么，会不会覆盖呢？
 
会的，在外面引入的数据会把自己自带的数据给替换掉
 
做个小例子吧，比如说现在要这么做，一个组件里呢，我要显示这样的东西，如果你有name属性，我就显示你的名字，没有就显示‘world’
 
三元表达式、  组件的方法返回值赋值变量去使用变量  、   直接调用这个方法    、     || 运算符
 
**demo：**
 
 
	var App=React.createClass({
		getName:function(){
			if(this.props.name){
				return this.props.name;
			}else{
				return 'world'
		}
	},
	render:function(){
		var name=this.getName();
		return (
			<div>
				<span>Hello,{this.props.name?this.props.name:'world'}</span><br/>
				<span>Hello,{name}</span><br/>
				<span>Hello,{this.getName()}</span><br/>
				<span>Hello,{this.props.name||'name'}</span><br/>
				<span>Hello,
				{(function(obj){
					if(obj.props.name){
						return obj.props.name;
					}else{
						return 'world'
					}
				})(this)}
				</span><br/>
				</div>
				)
			}
		})
		var props={name:"allen",age:18};
		ReactDOM.render(<App {...props}/>,document.getElementById('app'))
	 
 
状态（state）在组件内部设置或者更改，组件内部通过this.state获得

state，就这个货  指的呢是事物所处的状况  为了让状态改变的时候，去改变结果
 
状态呢是由事物自行处理的，不断变化的
 
在react中，state属于组件的自有属性
 
getInitialState初始化每个实例特有的状态
 
`setState({name:"red"})`来更新组件状态 
 
每次状态更新之后，react会对组件进行一个diff的算法，查看一下是否需要更新dom   如果需要更新dom，就会重新执行render方法
 
	var App1=React.createClass({
		render:function(){
			return (
				<div>
					<h4 >app1 {this.props.name}</h4>
				</div>
				)
		}
	})
		var App2=React.createClass({
			getInitialState:function(){
				return {
					name:'allen',
					age:18
				}
			},
		changeState:function(){
			this.setState({name:'tom'})
		},
		inpChangeState:function(ev){
			this.setState({name:ev.target.value})
		},
		render:function(){
			return (
				<div>
					<h2>{this.state.name}</h2>
					<h2>{this.state.age}</h2>
					<button onClick={this.changeState}>change</button>
					<input type="text" onChange={this.inpChangeState}/>
					<App1 {...this.state}/>
				</div>
			)
		}
		})
	ReactDOM.render(<App2/>,document.getElementById('app'))
 
 
**refs** 
 
可以dom结构上dom做标记
 
    this.refs.refname

那么这两个东西，状态和属性都有什么相同点和不同点呢，来研究一下
 
相似点：都是纯js对象，都会触发render更新，都具有确定性（状态/属性相同，结果相同）
 
不同点：  

属性能从父组件获取，状态不能

属性可以由父组件修改，状态不能

属性能在内部设置默认值     ，状态也可以

属性不在组件内部修改   ，状态要改

属性能设置子组件初始值  ，状态不可以

属性可以修改子组件的值，状态不可以
 
状态只和自己相关，由自己维护

属性不要自己修改，可以从父组件获取，也可以给子组件设置
 
组件在运行时自己需要修改的数据其实就是状态而已

**设置属性值时使用双花括号**

由于 this.props 和 this.state 都用于描述组件的特性，可能会产生混淆。一个简单的区分方法是，this.props 表示那些一旦定义，就不再改变的特性，而 this.state 是会随着用户互动而产生变化的特性。

循环输出数据//

	var Tiezhu=React.createClass({
		render:function(){
	
			var arr=[1,2,3,4,5,6,7,8,9,10];
	
			var aarry=[];
	
			for(var i=0;i<arr.length;i++){
	
				aarry.push(<h1>{arr[i]}</h1>)
			}
			return (
				<div>
					{aarry}
				</div>
			)
		}
	})
	ReactDOM.render(<Tiezhu/>,document.getElementById("app2"));

refs  
 
可以dom结构上dom做标记
 
    this.refs.refname

相似点：都是纯js对象，都会触发render更新，都具有确定性（状态/属性相同，结果相同）
 
不同点：  属性能从父组件获取，状态不能
               属性可以由父组件修改，状态不能
               属性能在内部设置默认值     ，状态也可以
               属性不在组件内部修改   ，状态要改
               属性能设置子组件初始值  ，状态不可以
               属性可以修改子组件的值，状态不可以
 
状态只和自己相关，由自己维护
属性不要自己修改，可以从父组件获取，也可以给子组件设置
 
组件在运行时自己需要修改的数据其实就是状态而已
