---
title: 'react与redux,dva'
date: 2017-12-05 11:55:38
tags: React
---
**一.  State和 Props**

  state是状态机。

  应该包括：那些可能被组件的事件处理器改变并触发用户界面更新的数据，譬如需要对用户输入,服务器请求或者时间变化等作出响应。

  不应该包括：计算所得数据、React组件（在render()里使用props和state来创建它）、基于props的重复数据（尽可能保持用props来做作为唯一的数据来源，把props保存到state中的有效的场景是需要知道它以前的值得时候,因为未来的props可能会变化）。

<!-- more -->

  props： 父级向子级传递数据的方式。
     
**二.  有状态组件和无状态组件（纯函数组件）**

  有状态组件

  通过React.createClass或者es6的class继承React.Component创建的组件。特性：具备完整的生命周期及实例化过程、支持this及ref指向。

  无状态组件：

  即stateless component( pure function Component)。以函数返回值方式方式创建的组件。特点： 无实例化过程及生命周期、无this及ref指向、函数接受props及context两个参数。

  实践模式

  创建多个只负责渲染数据的无状态(stateless)组件,在他们的上层创建一个有状态(stateful)组件并把它的状态通过props传给子级。有状态的组件封装了所有的用户交互逻辑，state中处理状态的变化, 而这些无状态组件只负责声明式地渲染数据.
     
 
**三.  受控组件、非受控组件及混合组件**

  有许多的web组件可以被用户的交互发生改变，比如：`<input>，<select>`。这些组件可以通过输入一些内容或者设置元素的value属性来改变组件的值。但是，因为React是单向数据流绑定的，这些组件可能会变得失控：

  1.一个维护它自己state里的value值的`<Input>`组件无法从外部被修改

  2.一个通过props来设置value值的`<Input>`组件只能通过外部控制来更新。

  受控组件：

  一个受控的`<input>`应该有一个value属性。渲染一个受控的组件会展示出value属性的值。 

  一个受控的组件不会维护它自己内部的状态，组件的渲染单纯的依赖于props。也就是说，如果我们有一个通过props来设置value的`<input>`组件，不管你如何输入，它都只会显示props.value。换句话说，你的组件是只读的。

  在处理一个受控组件的时候，应该始终传一个value属性进去，并且注册一个onChange的回调函数让组件变得可变。

  非受控组件

  一个没有value属性的`<Input>`就是一个非受控组件。通过渲染的元素，任意的用户输入都会被立即反映出来。非受控的`<Input>`只能通过OnChange函数来向上层通知自己被用户输入的更改。

  混合组件

  同时维护props.value和state.value的值。props.value在展示上拥有更高的优先级，state.value代表着组件真正的值。

  目的：

  1、支持传入值； 

  2、可控：组件外部修改props可改变input组件的真实值及显示值； 

  3、非可控：输入框中输入值，可同时改变input组件的真实值及显示值。

**四.  redux和dva**

  Redux

  1. Actions、Reducers 和 Store
 
  action 可以理解为应用向 store 传递的数据信息（一般为用户交互信息）。在实际应用中，传递的信息可以约定一个固定的数据格式，比如: Flux Standard Action。 dispatch(action) 是一个同步的过程：执行 reducer 更新 state -> 调用 store 的监听处理函数。如果需要在 dispatch 时执行一些异步操作（fetch action data），可以通过引入 Middleware 解决。

  reducer 实际上就是一个函数：(previousState, action) => newState。用来执行根据指定 action 来更新 state 的逻辑。reducer 不存储 state, reducer 函数逻辑中不应该直接改变 state 对象, 而是返回新的 state 对象。
 
  store 是一个单一对象，redux中只有唯一一个store实例。主要作用：
       1. 管理应用的 state
       2. 通过 store.getState() 可以获取 state
       3. 通过 store.dispatch(action) 来触发 state 更新
       4. 通过 store.subscribe(listener) 来注册 state 变化监听器



  2. Dva

数据流向

数据的改变发生通常是通过用户交互行为或者浏览器行为（如路由跳转等）触发的，当此类行为会改变数据的时候可以通过 dispatch 发起一个 action，如果是同步行为会直接通过 Reducers 改变 State ，如果是异步行为（副作用）会先触发 Effects 然后流向 Reducers 最终改变 State，所以在 dva 中，数据流向非常清晰简明，并且思路基本跟开源社区保持一致（也是来自于开源社区）。

Modele

State

type State = any

State 表示 Model 的状态数据，通常表现为一个 javascript 对象（当然它可以是任何值）；操作的时候每次都要当作不可变数据（immutable data）来对待，保证每次都是全新对象，没有引用关系，这样才能保证 State 的独立性，便于测试和追踪变化。
在 dva 中你可以通过 dva 的实例属性 _store 看到顶部的 state 数据，但是通常你很少会用到:
	const app = dva();
	console.log(app._store); // 顶部的 state 数据

Action

type AsyncAction = any

Action 是一个普通 javascript 对象，它是改变 State 的唯一途径。无论是从 UI 事件、网络回调，还是 WebSocket 等数据源所获得的数据，最终都会通过 dispatch 函数调用一个 action，从而改变对应的数据。action 必须带有 type 属性指明具体的行为，其它字段可以自定义，如果要发起一个 action 需要使用 dispatch 函数；需要注意的是 dispatch 是在组件 connect Models以后，通过 props 传入的。

	dispatch({
	  type: 'add',
	});

dispatch 函数

type dispatch = (a: Action) => Action

dispatching function 是一个用于触发 action 的函数，action 是改变 State 的唯一途径，但是它只描述了一个行为，而 dipatch 可以看作是触发这个行为的方式，而 Reducer 则是描述如何改变数据的。
在 dva 中，connect Model 的组件通过 props 可以访问到 dispatch，可以调用 Model 中的 Reducer 或者 Effects，常见的形式如：

	dispatch({
	  type: 'user/add', // 如果在 model 外调用，需要添加 namespace
	  payload: {}, // 需要传递的信息	});

Reducer

type Reducer<S, A> = (state: S, action: A) => S

Reducer（也称为 reducing function）函数接受两个参数：之前已经累积运算的结果和当前要被累积的值，返回的是一个新的累积结果。该函数把一个集合归并成一个单值。

Reducer 的概念来自于是函数式编程，很多语言中都有 reduce API。如在 javascript 中：

	[{x:1},{y:2},{z:3}].reduce(function(prev, next){
	    return Object.assign(prev, next);
	})
	//return {x:1, y:2, z:3}

在 dva 中，reducers 聚合积累的结果是当前 model 的 state 对象。通过 actions 中传入的值，与当前 reducers 中的值进行运算获得新的值（也就是新的 state）。
需要注意的是 Reducer 必须是纯函数，所以同样的输入必然得到同样的输出，它们不应该产生任何副作用。并且，每一次的计算都应该使用immutable data，这种特性简单理解就是每次操作都是返回一个全新的数据（独立，纯净），所以热重载和时间旅行这些功能才能够使用。

Effect

Effect 被称为副作用，在我们的应用中，最常见的就是异步操作。它来自于函数编程的概念，之所以叫副作用是因为它使得我们的函数变得不纯，同样的输入不一定获得同样的输出。

dva 为了控制副作用的操作，底层引入了redux-sagas做异步流程控制，由于采用了generator的相关概念，所以将异步转成同步写法，从而将effects转为纯函数。至于为什么我们这么纠结于 纯函数，如果你想了解更多可以阅读Mostly adequate guide to FP，或者它的中文译本JS函数式编程指南。

Subscription

Subscriptions 是一种从 源 获取数据的方法，它来自于 elm。

Subscription 语义是订阅，用于订阅一个数据源，然后根据条件 dispatch 需要的 action。数据源可以是当前的时间、服务器的 websocket 连接、keyboard 输入、geolocation 变化、history 路由变化等等。

	import key from 'keymaster';
	...
	app.model({
	  namespace: 'count',
	  subscriptions: {
	    keyEvent(dispatch) {
	      key('⌘+up, ctrl+up', () => { dispatch({type:'add'}) });
	    },
	  }
	});

Router

这里的路由通常指的是前端路由，由于我们的应用现在通常是单页应用，所以需要前端代码来控制路由逻辑，通过浏览器提供的 History API 可以监听浏览器url的变化，从而控制路由相关操作。

dva 实例提供了 router 方法来控制路由，使用的是react-router。

	import { Router, Route } from 'dva/router';
	app.router(({history}) =>
	  <Router history={history}>
	    <Route path="/" component={HomePage} />
	  </Router>
	);

Route Components

在组件设计方法中，我们提到过 Container Components，在 dva 中我们通常将其约束为 Route Components，因为在 dva 中我们通常以页面维度来设计 Container Components。

所以在 dva 中，通常需要 connect Model的组件都是 Route Components，组织在/routes/目录下，而/components/目录下则是纯组件（Presentational Components）。
 
**五.  hoc**

  HOC(全称Higher-order component)是一种React的进阶使用方法，主要还是为了便于组件的复用。HOC就是一个方法，获取一个组件，返回一个更高级的组件。

  在React开发过程中，发现有很多情况下，组件需要被"增强"，比如说给组件添加或者修改一些特定的props，一些权限的管理，或者一些其他的优化之类的。而如果这个功能是针对多个组件的，同时每一个组件都写一套相同的代码，明显显得不是很明智，所以就可以考虑使用HOC。

  一个最简单的HOC实现是这个样子的：

  HOC可以做什么？

  代码复用，代码模块化

  增删改props

  渲染劫持

  1.增删改props

  可以通过对传入的props进行修改，或者添加新的props来达到增删改props的效果。

  比如你想要给wrappedComponent增加一个props，可以这么搞：

  这样，你就可以在你的组件中使用message这个props:

  2.渲染劫持

  这里的渲染劫持并不是你能控制它渲染的细节，而是控制是否去渲染。由于细节属于组件内部的render方法控制，所以你无法控制渲染细节。

  比如，组件要在data没有加载完的时候，现实loading...，就可以这么写：
 
  这个样子，在父级没有传入data的时候，这一块儿就只会显示loading...,不会显示组件的具体内容
 
**六. 项目中的实践**

  1. 合理使用有状态组件及无状态组件。在使用redux或者dva的场景下，理论上所有的组件都可以封装为无状态组件（少数需要生命周期控制或者上文提到的混合式组件除外），model中封装数据、异步effects及同步reducers，通过connect绑定到对应的组件上。
  最佳实践： router中getcomponent中定义的组件我们称之为路由组件，一般路由组件会通过connect绑定model中定义的state及组件中定义的方法到该组件的props上。其他方式定义的为非路由组件，非路由组件尽量避免使用connect，而是通过路由组件或者其他上层通过props传递数据进行渲染。

  2. 理解subscription, effects及reducers中各自的功能职责。

  3. package.json中定义的dependency，需要深入研究，避免重复造轮子。

  4. 全局观及合理的组件规划。