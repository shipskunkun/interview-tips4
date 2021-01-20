#### 7-1 React使用-考点串讲 (09:58)

setState 是同步还是异步？

####  7-2 JSX基本知识点串讲 (10:43)

1. react 中 state 对应 vue 中data

2. react 插值，{ }，  vue中 {{ }}

3. vue 中动态数据， :src = 'a.png'

   react中没有，react 中看是否是动态数据，就看后面有没有括号 { } 。

4. react 中不能使用 class , 关键字， className

5. style

   两种方法：

   1.  定义变量， 花括号内使用变量 {}

   2. 直接写, 所以有两个 {{ }}

      ```react
      // style
      const styleData = { fontSize: '30px',  color: 'blue' }
      const styleElem = <p style={styleData}>设置 style</p>
      // 内联写法，注意 {{ 和 }}
      // const styleElem = <p style={{ fontSize: '30px',  color: 'blue' }}>设置 style</p>
      ```

      

6. 原生html

   ```react
   // 原生 html
   const rawHtml = '<span>富文本内容<i>斜体</i><b>加粗</b></span>'
   const rawHtmlData = {
     __html: rawHtml // 注意，必须是这种格式
   }
   const rawHtmlElem = <div>
           <p dangerouslySetInnerHTML={rawHtmlData}></p>
           <p>{rawHtml}</p>
         </div>
         return rawHtmlElem
   ```

####  7-3 JSX如何判断条件和渲染列表 (09:16)

两种方法，if else 或者 三元表达式

```react
const blackBtn = <button className="btn-black">black btn</button>
const whiteBtn = <button className="btn-white">white btn</button>

// if else
if (this.state.theme === 'black') {
	return blackBtn
} else {
	return whiteBtn
}

// 三元运算符
return <div>
{ this.state.theme === 'black' ? blackBtn : whiteBtn }
</div>
```

列表渲染

```react
return <ul>
{ /* vue v-for */
this.state.list.map(  // 生成 li 构成的新数组，通过{} 展开数组
  (item, index) => {
    // 这里的 key 和 Vue 的 key 类似，必填，不能是 index 或 random
    return <li key={item.id}>
    	index {index}; id {item.id}; title {item.title}
    </li>
    }
	)
}
</ul>
```



####  7-4 React事件为何bind this (11:14)

#### 7-5 React事件和DOM事件的区别 (08:31)

什么时候bind this？

1. 在 constructor 中定义
2. 在绑定的事件的时候，调用 bind(this)   // 如果需要传递参数的话，就需要绑定的时候调用
3. 使用箭头函数



```
TypeError [ERR_INVALID_ARG_TYPE]: The "path" argument must be of type string. Received undefined
```

报错，原因：

react-scripts 版本低，升级版本



为什么在constructor 中效率高？

因为只执行一次，如果在绑定的时候bind ，每次调用方法都要重新修改this

但是：如果要传递参数，就一定要bind，其他方法都没效果



事件中的 event 不是原生事件对象，是react 封装的对象



问题总结：

1. event.target 和 event.currentTarget 区别
   1. 前者是真正点击元素，后者是绑定事件的元素

2. react event 和原生event区别？

   1. 原生是 MouseEvent， react 打印出来是合成事件 SyntheticEvent

3.  react 中怎么找到原生事件？

   1. 事件绑定在 document中， currentTarget 是 document

      

```react
console.log('target', event.target) // 指向当前元素，即当前元素触发
console.log('current target', event.currentTarget) // 指向当前元素，假象！！！

// 注意，event 其实是 React 封装的。可以看 __proto__.constructor 是 SyntheticEvent 组合事件
console.log('event', event) // 不是原生的 Event ，原生的 MouseEvent
console.log('event.__proto__.constructor', event.__proto__.constructor)

console.log('nativeEvent', event.nativeEvent)  //打印出来是MouseEvent
console.log('nativeEvent target', event.nativeEvent.target)  // 指向当前元素，即当前元素触发
console.log('nativeEvent current target', event.nativeEvent.currentTarget) // 指向 document ！！！

// vue event是原生事件
<button data-get="数据按钮" @click="getRvent($event)">获取事件对象</button>
```



####  7-6 React表单知识点串讲 (07:57)

label 中不能用关键字 for， 使用 htmlFor 代替

```react
<label htmlFor="inputName">姓名：</label> {/* 用 htmlFor 代替 for */}
<input id="inputName" value={this.state.name} onChange={this.onInputChange}/>            
```

什么是受控组件？

​	组件的值受到state 控制

非受控组件？

​	表单的值不受state控制



####  7-7 React父子组件通讯 (08:41)

props传递数据

props传递函数

props类型检查



如何传递给子组件？

```react
<Input submitTitle={this.onSubmitTitle}/>  //父组件中定义变量

//子组件中通过 constructor 中 props接受
class Input extends React.Component {
    constructor(props) {
        super(props)
        this.state = {
            title: ''
        }
    }
  // 使用：
  ...
  const { submitTitle } = this.props
}
```

子组件中如何执行父组件中传入方法？

```
子组件通过props拿到父组件传入方法，直接执行该方法即可，子组件不必通过emit 触发父组件方法。

相当于在子组件中直接调用父组件中定义的方法。
```

​	

类型检查

```react
class List extends React.Component {}
//通过类名 . propTypes
List.propTypes = {
    list: PropTypes.arrayOf(PropTypes.object).isRequired
}
```

是否更新

```react
shouldComponentUpdate(nextProps, nextState) {
        if (nextProps.text !== this.props.text
            || nextProps.length !== this.props.length) {
            return true // 可以渲染
        }
        return false // 不重复渲染
    }

    // React 默认：父组件有更新，子组件则无条件也更新！！！
    // 性能优化对于 React 更加重要！
    // SCU 一定要每次都用吗？—— 需要的时候才优化
```



####  7-8 setState为何使用不可变值 (14:44)



1. 不可变值。
2. 可能是异步更新
3. 可能会被合并



```react
// 函数组件（后面会讲），默认没有 state
class StateDemo extends React.Component {}

```

1. 什么是不可变值？

​	当我们修改state的值，不要直接操作 state

​	需要用一个变量，把state赋值给 这个变量，这个变量改变后，把这个值赋值给需要修改的state变量。

​	如果我们的state修改变量是一个数组时，不想赋值，修改，再赋值，直接对改数组操作，注意这个操作不能影响之前 state的值，比如 arr 的push操作，会影响原数据，不行。

​	一句话，在setState 之前，不能修改state值。

​	注意，不能直接对 this.state.list 进行 push pop splice 等，这样违反不可变值

```react
this.state.count++ // 错误
count: this.state.count + 1  //正确

arr.concat(100)   //arr 值没有改变

arr.push(100)  //arr 值改变
```

哪些操作是可以的？

数组，对象

```react
// // 不可变值（函数式编程，纯函数） - 数组
const list5Copy = this.state.list5.slice()
list5Copy.splice(2, 0, 'a') // 中间插入/删除
this.setState({
    list1: this.state.list1.concat(100), // 追加
    list2: [...this.state.list2, 100], // 追加
    list3: this.state.list3.slice(0, 3), // 截取
    list4: this.state.list4.filter(item => item > 100), // 筛选
    list5: list5Copy // 其他操作
})
// // 注意，不能直接对 this.state.list 进行 push pop splice 等，这样违反不可变值

// // 不可变值 - 对象
this.setState({
    obj1: Object.assign({}, this.state.obj1, {a: 100}),
    obj2: {...this.state.obj2, a: 100}
})
// // 注意，不能直接对 this.state.obj 进行属性设置，这样违反不可变值

```



####  7-9 setState是同步还是异步 (07:01)

可以理解为类似 ajax，纯粹的 setState 是异步操作

我们在 setState 后，再访问state 值，是没有修改过的state值

那么，如何拿到 修改后的 state值？

在 callback 函数中拿修改后的 state 值



```react
this.setState({
  count: this.state.count + 1
}, () => {
  // 联想 Vue $nextTick - DOM
  console.log('count by callback', this.state.count) // 回调函数中可以拿到最新的 state
})
console.log('count', this.state.count) // 异步的，拿不到最新值


// setTimeout 中 setState 是同步的
setTimeout(() => {
  this.setState({
    count: this.state.count + 1
  })
  console.log('count in setTimeout', this.state.count)
}, 0)

// 自己定义的 DOM 事件，setState 是同步的
componentDidMount() {
  document.body.addEventListener('click', this.bodyClickHandler)
}
bodyClickHandler = () => {
  this.setState({
    count: this.state.count + 1
  })
  console.log('count in body event', this.state.count)
}
```



总结：直接使用异步，在callback 函数中，setTimeout 或者自定义事件中，是同步的。

####  7-10 setState合适会合并state (07:22)

setState 中初始化，要放在 constructor 中。

如何理解？

setState 接受的是对象，会被合并

setState 接受值为函数，操作不会被合并。

```react
类似 Obect.assign({a: 1 }, {a: 1} ) 结果没变

// // 传入对象，会被合并（类似 Object.assign ）。执行结果只一次 +1
this.setState({
count: this.state.count + 1
})
this.setState({
count: this.state.count + 1
})
this.setState({
count: this.state.count + 1
})

// 传入函数，不会被合并。执行结果是 +3
this.setState((prevState, props) => {
  return {
    count: prevState.count + 1
  }
})
this.setState((prevState, props) => {
  return {
    count: prevState.count + 1
  }
})
this.setState((prevState, props) => {
  return {
    count: prevState.count + 1
  }
})

```



####  7-11 React组件生命周期 (05:49)

![img](https://img2018.cnblogs.com/blog/1207967/201901/1207967-20190112160234832-1702651571.jpg)

React v16.3之前的生命周期函数（图中实际上少了componentDidCatch)，这个生命周期函数的组合在Fiber（[React Fiber是什么](https://zhuanlan.zhihu.com/p/26027085)）之后就显得不合适了，因为，**如果要开启async rendering，在render函数之前的所有函数，都有可能被执行多次。**长期以来，原有的生命周期函数总是会诱惑开发者在render之前的生命周期函数做一些动作，现在这些动作还放在这些函数中的话，有可能会被调用多次，这肯定不是你想要的结果。

![img](https://pic3.zhimg.com/v2-48e4dd255a7690beaef4d496ac6af7ca_r.jpg)



####  7-12 React基本使用-知识点总结和复习 (02:50)

####  7-13 React函数组件和class组件有何区别 (06:36)

高级特性：

1. 函数组件

2. 非受控组件

3. portals

4. context

5. 异步组件

6. 性能优化

7. 高阶组件HOC

8. Render Props




函数组件和class 声明的组件有何不同？

函数组件接受 props数据，返回 JSX内容， 没有state 声明、没有constructor， 没有定义 render 函数、没有定义方法 等

1. 纯函数，输入props，输出jsx
2. 没有实例，没有生命周期，没有state
3. 不能扩展其他方法

####  7-14 什么是React非受控组件 (09:18)

受控组件和非受控

1. ref
2. defaultValue 和 defaultChecked
3. 手动操作DOM元素



自己理解：

state 用于设置初始值， input 组件设置 defaultValue， checkbox 中设置  defaultChecked

我们不会监听input操作，获取e.currentTarget 的方式，去修改 state的值，所以我们不从 state 中拿值。

我们通过ref 获取dom元素上的值，这个值是正确的。



应用场景：

一句话：当不能通过state获取值，必须通过dom去获取值

1. 必须手动操作dom元素，setState实现不了
2. 文件上传
3. 富文本编辑器，传入dom元素



有限使用受控组件，数据驱动视图

当必须操作dom时，再使用非受控组件



####  7-15 什么场景需要用React Portals (05:37)

传动门，大门。

组件默认会按照既定层次嵌套渲染

如何让组件渲染到父组件之外？



之前所有的元素都是渲染在 root 节点之下，index.js中

```react
ReactDOM.render(<App />, document.getElementById('root'));
```

如何渲染在 root 外面？和 root 在dom上是同一级别关系？放在body上？

使用

```react
return ReactDOM.createPortal(
	<div className="modal">{this.props.children}</div>,
	document.body // DOM 节点
)
```



使用场景：

父组件，设置bfc，想要子组件逃离父组件

父组件z-index太小

fixed需要放在body第一层



一般都是处理css兼容性，布局等问题。



####  7-16 是否用过React Context (12:22)

公共信息（语言、主题）如何传递给每个组件？

props太繁琐

使用redux？小题大做。



看代码后自己的理解：

```react
1. 首先，通过 createContext 创建 context组件。
const ThemeContext = React.createContext('light')

2. 在根组件通过   context组件名.Provider 给子组件传递 context
<ThemeContext.Provider value={this.state.theme}></ThemeContext.Provider>
  
3. 子组件指定 contextType = context组件名 ，子组件中访问 context
ThemeLink.contextType = ThemeContext // 指定 contextType 读取当前的 theme context。
const theme = this.context // React 会往上找到最近的 theme Provider，然后使用它的值。


函数组件 和 class 组件时不同的。
<ThemeContext.Consumer> // 函数组件通过 consumer 方式
```

面试：

1. 应用场景
2. 核心api，如何生产数据，如何消费数据

####  7-17 React如何异步加载组件 (07:33)

import 

React.lazy

React.Suspense ??

```react
const ContextDemo = React.lazy(() => import('./ContextDemo'))

<React.Suspense fallback={<div>Loading...</div>}>
	<ContextDemo/>
</React.Suspense>
```



#### 7-18 React性能优化-SCU的核心问题在哪里 (06:55)

性能优化对于react ，相较于 vue更为重要

state 不可变值



shouldComponentUpdate

pureComponent 和 React.memo

immutable.js



shouldComponentUpdate， 默认返回true，接受的参数，更新后的  props , nextProps 和 更新后的 state， nextState

如果指定的属性更新，那么判断，如果更新，返回true

否则，返回false

```react
// 演示 shouldComponentUpdate 的基本使用
shouldComponentUpdate(nextProps, nextState) {
  if (nextState.count !== this.state.count) {
    return true // 可以渲染
  }
  return false // 不重复渲染
}
```

问题？为啥要暴露这个方法？

为啥不像vue一样，改变就更新，不改变就 不更新



####  7-19 React性能优化-SCU默认返回什么 (08:51)

默认返回 true

我们看propsDemo 例子，父组件通过input 修改state数据，footer组件没有接受父组件任何值。

但是 footer组件也重新 update了，这不是我们想看到的

只要父组件 state 改变，子组件就更新。

```react
// 父组件
return <div>
  <Input submitTitle={this.onSubmitTitle}/>
  <List list={this.state.list}/>
  <Footer/>
</div>

shouldComponentUpdate(nextProps, nextState) {
  if (nextProps.text !== this.props.text) {
    return true // 可以渲染
  }
  return false // 不重复渲染
}

// React 默认：父组件有更新，子组件则无条件也更新！！！
// 性能优化对于 React 更加重要！
// SCU 一定要每次都用吗？—— 需要的时候才优化
```





####  7-20 React性能优化-SCU一定要配合不可变值 (09:17)

总结：

scu 默认返回true, react 默认重新渲染所有子组件

不可变值一起使用

可以先不用scu， 有性能问题时再考虑使用



####  7-21 React性能优化-PureComponent和memo (03:13)

纯组件，PureComponent， 对state 和 props 进行浅比较，只比较第一层数据

深比较，耗费性能

函数组件使用 memo

class 组件使用PureComponent

```react
class List extends React.PureComponent { 
  shouldComponentUpdate() {/*浅比较*/}
}

memo

function areEqual(preProps, nextProps) {
  
}
export default React.momo(MyComponent, areEqual)
```



####  7-22 React性能优化-了解immutable.js (03:52)

彻底拥抱，不可变值

基于共享数据（不是深拷贝），速度比较好

理解：

完成层次比较深的数据的 深拷贝，然后赋值给 state，但是速度比较快



####  7-23 什么是React高阶组件 (12:31)

面试：

了解，并应用过，在项目中使用过



mixin, 在react 中弃用

高阶组件 HOC， high order component

render props



HOC : 类似工厂，接受组件作为参数，返回一个新组件，类似装饰器

复杂的逻辑部分在高阶组件之中，返回的新数组就有了公共逻辑。

```react
// 高阶组件
// 复杂逻辑写在这里
const withMouse = (Component) => {
    class withMouseComponent extends React.Component {

      <div style={{ height: '500px' }} onMouseMove={this.handleMouseMove}>
        {/* 1. 透传所有 props 2. 增加 mouse 属性 */}
        <Component {...this.props} mouse={this.state}/>
      </div>
 	return withMouseComponent
}   
  
const App = (props) => { }

export default withMouse(App) // 返回高阶函数
```



redux  connect 是高阶组件

如何理解？面试的时候可以说出

```react
// connect 是高阶组件
connet(mapStateToProps, mapDispatchToProps)(TodoList)

两个参数，第一个参数，生成高阶组件，第二个参数，高阶组件接受组件作为参数

todolist 组件中会 props 高阶组件中的 mapStateToProps, mapDispatchToProps

```



####  7-24 什么是React Render Props (08:55)

我理解：和 高阶组件反过来，高阶组件如何扩充组件？把逻辑写在高阶组件中，传入组件为参数，渲染扩充后的组件

子组件被 HOC 高阶组件包裹



render props，子组件包裹高阶组件，扩充组件，把高阶组件嵌入 app 中，传入render 组件



####  7-25 React高级特性考点总结 (02:24)

非受控组件：自己操作dom元素

portals：在根组件外

context： 透传给组件

异步组件：分步加载

性能优化，SCU

高阶组件：工厂函数

render props

####  7-26 Redux考点串讲 (03:39)

单向数据流，画图

react-redux ，如何连接

异步action

中间件, redux-thunk, redux-saga



####  7-27 描述Redux单项数据流 (03:22)

页面操作，产生一个action

通过 dispatch 触发 reducer 修改对应的 state

state修改，触发视图更新



#### 7-28 串讲react-redux知识点 (05:14)

通过在app外层套用 provider ，每个组件拥有store

四个要点：

provider

connect

mapStateToProps

mapDispatchToProps   自定义事件


createStore 接受 reducers 为参数，创建store并传递给provider, 在根节点上，使得每个组件拿到store

reducers 通过 combineReducers  合并成一个



```react
import React from 'react'
import { Provider } from 'react-redux'
import { createStore } from 'redux'
import todoApp from './reducers'
import App from './components/App'

let store = createStore(todoApp)

export default function () {
    return <Provider store={store}>
        <App />
    </Provider>
}


const todoApp = combineReducers({
  todos,
  visibilityFilter
})



```

组件中如何 dispatch action？



```react
// 函数组件，接收 props 参数， 每个组件有store属性
let AddTodo = ({ dispatch }) => {
  // dispatch 即 props.dispatch
  
// connect 高阶组件 ，将 dispatch 作为 props 注入到 AddTodo 组件中
 dispatch(addTodo(input.value))
AddTodo = connect()(AddTodo)
  
// connect 高阶组件，将 state 和 dispatch 注入到组件 props 中
const VisibleTodoList = connect(
  mapStateToProps,
  mapDispatchToProps
)(TodoList)  
  
```


####  7-29 Redux action如何处理异步 (03:32)

同步action，返回一个对象，type + data

异步action，使用redux-thunk 中间件，使用中间件之后，返回 一个函数，参数是 dispatch，dispatch 参数是 同步的action 对象



其他几个中间件：

redux-promise, redux-saga





####  7-30 简述Redux中间件原理 (07:07)



redux 单选数据量

view  事件， dispatch，action，reducer， view

想要插入自己的逻辑，在哪里插入，只能在dispatch 过程中添加。

案例：redux中间件，logger

思路：

1. 使用变量保存store.dispatch ，
2. 给 store.dispatch 重新赋值
3. 然后执行 老的方法即可



redux 数据流图，加上，中间件之后的图。



####  7-31 串讲react-router知识点 (04:02)

模式，hash、history

路由配置，动态路由，懒加载

```react
Switch
	路由 ，匹配的组件
<HashRouter>
  <Switch>
    <Route path="">
      <Home/>
    </Route>
  </Switch>
</HashRouter>
```

动态路由：

​	path="/child/:id"



路由跳转：

​	Link  to=""

​	或者：history.push



懒加载：

​	lazy()  import ,   suspense

####  7-32 React使用-考点总结 (10:29)

