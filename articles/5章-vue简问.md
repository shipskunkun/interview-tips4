
## 观点

5-1   

上班前半个小时，每个博客网上去看看，然后收集起来

网上说的什么9种，8种方法，不用看，主要是面试你过来干活的，正常产出，不掉链子

不是理想主义，不用考查细节


## 5-1 


1. v-show 和 v-if 的区别

	
		v-show 是通过 css display 控制显示和隐藏，
		
		v-if 组件真正的销毁和渲染，而不是显示和隐藏
		
		频繁切换显示状态使用 v-show，否则用 v-if


2. 为何 在 v-for 中使用 key

		必须使用 key, 且不能是 index 和 random
		
		diff 算法中 通过 tag 和 key 判断，是否是 sameNode
		
		减少渲染次数，提高渲染性能
	
3. vue 组件生命周期，包括父子组件

4. vue 组件如何通讯（常见
	
		父子组件， props 和 $emit
		
		vuex
		
		自定义事件， event.$on  event.$off event.$emit
	
5. vue 组件渲染和更新的过程

		三大模块，
		
		响应式，监听属性变化 get、setter
		
		收集依赖，watcher 
		
		模板渲染，render，vdom 

	
		render => (touch ) getter
		
		setter =>  (notify) watcher => render 
	

## 5-2 


1. v-model 实现原理

	
		input 中，展示的 value = this.name
		
		绑定input 事件，oninput,   this.name =  $event.target.value
		
		data 更新触发 re-render




2. 对 mvvm 的理解

		关键：可以尝试自己画个图
	
3. computed  特点

		缓存，data 不变不会重新计算
		
		提高性能
	
4.  为何组件data 必须是一个函数

	数据隔离。
		
	
		每个文件，.vue 编译之后，是一个类，
		
		使用组件，实际上是一个对这个类的实例化。 
		
		如果 data 不是函数，每个组件的  data 就共享了。
		
		实例化后，data， 都会执行函数，就在不同闭包之中，不会相互影响


5. ajax 请求放在哪个生命周期

	####真的，面过了，而且还答错了。

		看情况，一般放在created 中就可以了，如果有依赖dom 必须存在的情况，就放到 mounted中

		mounted 中，js渲染完， 数据挂载完。  
		js 是单线程的，ajax 异步获取数据，   
		放在 mounted 之前， 没有用，还会更混乱。
	
## 5-3 

1. 如何将组建所有 props 传递给子组件？

	
		$props
		
		<user v-bind:="$props">
	
2. 如何自己实现 v-model?
3. 多个组件相同的逻辑，如何抽离？
	
		mixin  
		mixin 的缺点
	
	
4. 何时使用异步组件？
	
		加载大组件 component{ top: ()=> import  }
		
		路由异步加载
	
5. 何时使用 keep-alive

		缓存组件，不需要重复渲染的组件  
		多个静态tab页的切换  
		优化性能  
	
## 5-4

1. 何时使用 beforeDestory

		1. 解绑自定义事件， event.$off, 
		
		2. 清楚定时器
		
		3. 解绑自定义的dom 事件，addEventLisener, 如 window scroll
	
2. 什么是作用域插槽？

		slot 子组件，把data 传给 使用 slot的地方
		
		注意，数据定义是在 子组件中， 向外传出来数据，

3.  action 和 mutation 有何 区别
	
		action 中处理异步，mutation中不可以
		
		mutation 做原子操作
		
		action 可以整合多个 mutation
	
	
4. Vue router

		hash 默认
		
		h5 history （需要服务端支持
		
		实现原理？
		
		两种比较
	
5. vue-router 异步加载？

		component: () => import 

## 5-5 

1. vnode 描述一个 dom 结构

		{
			tag: 'div',
			props: {
				className: 'container',
				id: 'div1'
			},
			children: [
				{
					tag: 'p',
				 	children: 'vdom'
				},{
					...
				}]
		}

2. 监听data变化核心 API

		Object.defineProperty
		
		深度监听，监听数组？
		
		缺点


#### 3. 监听数组变化？（重要！
	
		Object.defineProperty 不能监听数组变化
		
		重新定义原型，重写 pop、push ，实现监听
		
		proxy 可以原生支持监听数组变化
	
4. 响应式原理
	
		主要讲两点：  
		监听data 变化的过程  
		组件渲染和更新的流程  

5. diff 算法 时间复杂度

		O(n)
		
		在 O(n3) 基础上做了一些调整
		
		哪些调整？
		
			比如只比较同级
			如果是type不相同，直接销毁重建
			通过tag和 key 判断是不是同一个 vnode
		
## 5-6
		
1. 简述 diff 算法过程
 
		patch(elem, vnode) 和 patch(vnode, newVnode)  
		patchVnode 、addVnodes 、 removeVnodes  
		updataChildren  
	
2. vue 是为何一步渲染，$nextTick 有什么效果
	
		异步渲染，合并data修改，提高渲染性能
		
		$nextTick 在 DOM 更新完之后，触发回调
	
3. vue 常见性能优化
		
	vue层级做的：
	
		1.合理使用 v-show v-if
		2. 合理使用 computed
		3. v-for 时加key， 避免和 v-if 一起使用， vor-for的优先级比v-if 高，每次v-for时，v-if 需要重新计算
		4. 合理使用keep-alive
		
		5.自定义事件，dom事件，及时销毁
		 
			防止内存泄露，越来越卡
			
			内存泄露就是系统回收不了那些分配出去但是又不使用的内存, 随着程序的运行,可以使用的内存就会越来越少,机子就会越来越卡
			
		6.data 层级不要太深，递归次数比较多
		7. 合理使用异步组件
	
	其他层级：
		
		8. vue-loader 在开发环境做预编译
		9. 前端通用性能优化，图片懒加载
		10. 使用ssr
		11. webpack层级优化
		 