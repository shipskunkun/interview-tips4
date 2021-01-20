##### 第9章 React 面试真题演练

#### 9-1 React真题演练-1-组件之间如何通讯 (03:58)

1. 父子组件如何通信

   父子组件 props

   自定义事件，和vue一样

   redux 

   context

   

2. JSX 本质

   createElement函数

   执行返回vnode

   

3. context 是什么，如何应用

   ​	父组件向其下所有子孙组件传递信息

   ​	简单的公共信息，主题色、语言等

   ​	复杂的数据，使用redux

   

4. shoulComponentUpdate

   性能优化

   配合不可变值一起使用，否则会出错

   

5. 单向数据流

   view ——> action ——>dispatch ——> reducer ——>state

   

####  9-2 React真题演练-2-ajax应该放在哪个生命周期 (04:34)

setState 中有setTimeout ，先后顺序



什么是纯函数？

	返回一个新值，没有副作用，不会修改其他值
	
	重点：不可变值
	
	arr1 = arr.slice

​	

ajax 生命周期

​	和vue 是一样的，componentDidMount, dom 渲染完之后的生命周期中

​	

为什么要key

​	和vue一样，是否是同一个samevode

​	减少渲染次数，提高渲染性能



#### 9-3 React真题演练-3-组件公共逻辑如何抽离 (02:15)

1. 函数组件和 class 组件区别

​	前者，纯函数，输入props，输出jsx

​	没有实例，没有生命周期，没有state

​	不能扩展其他方法



 2. 什么是受控组件？

     表单的值，受state控制

     需要自行监听onchange, 更新state 值，更新组件

     对比非受控组件

     

3. 何时使用异步组件？

     加载大组件

     路由懒加载

     

4. 多个组件有
	
   5. HOC 高阶组件

   6. render props

 		3. mixin, 已经被react废弃
	
5. redux 如何进行异步步请求
	
   9. 使用异步acion

 		2. redux-thunk

​     

####  9-4 React真题演练-4-React常见性能优化方式 (03:35)

异步组件懒加载

```
const home = lazy(()=> import('./routes/home'));
<Route path="" component={home}/>
```

pureComponent 有何区别

​	实现了浅比较的 shouldConponentUpdate

​	性能优化

​	结合不和变值使用



react 事件和dom事件区别

1. 所有事件挂载到document上
2. event不是原生的，是syntheticEvent 合成事件对象
3. dispatchEvent 机制



react 性能优化

1. 渲染列表加key

2. 自定义事件、dom事件在 unmount 生命周期及时销毁

3. 合理使用异步组件

4. 减少函数bind this 次数

   放在constructor 中

5. 使用 shouldComponentUpdate,  scu，    pureComponent  和 memo

6. 合理使用 immutable.js

7. webpack 层面优化

8. 前端通用的性能优化，图片懒加载等

9. 使用ssr

####  9-5 React真题演练-5-React和Vue的区别 (08:15)

1. 语法层面的

2. 共同点

   1. 都使用虚拟don

   2. 都提供了响应式和组件化的视图组件，数据驱动视图

   3. 都把注意力集中在核心库，其他功能如路由和全局状态管理交给其他库

      

3. 区别

   1. react 使用 jsx 拥抱js,  vue 使用 模板拥抱html
   2. react 函数式编程，vue 是声明式编程
      1. setState
   3. react 更多自力更生，vue 更适用于初学者
      1. v-for 和 render 中map



别人总结的：

https://www.cnblogs.com/yubin-/p/11537122.html

1. 数据是否可变

   ​	react整体是函数式的思想，在react中，是单向数据流，推崇结合immutable来实现数据不可变。react在setState之后会重新走渲染的流程，如果shouldComponentUpdate返回的是true，就继续渲染，如果返回了false，就不会重新渲染，PureComponent就是重写了shouldComponentUpdate，然后在里面作了props和state的浅层对比。



​	而Vue的思想是响应式的，也就是基于是数据可变的，通过对每一个属性建立Watcher来监听，当属性变化的时候，响应式的更新对应的虚拟dom。你可以理解为每一个组件都已经自动获得了 shouldComponentUpdate，并且没有上述的子树问题限制。Vue 的这个特点使得开发者不再需要考虑此类优化，从而能够更好地专注于应用本身。

1. 









