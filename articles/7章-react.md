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
2. 在绑定的事件的时候，调用 bind(this)
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

```react
console.log('event', event) // 不是原生的 Event ，原生的 MouseEvent
console.log('target', event.target) // 指向当前元素，即当前元素触发
console.log('current target', event.currentTarget) // 指向当前元素，假象！！！


console.log('nativeEvent', event.nativeEvent)
console.log('nativeEvent target', event.nativeEvent.target)  // 指向当前元素，即当前元素触发
console.log('nativeEvent current target', event.nativeEvent.currentTarget) // 指向 document ！！！


// vue event是原生事件
<button data-get="数据按钮" @click="getRvent($event)">获取事件对象</button>
```



event.target 和 event.currentTarget 区别？

前者是真正点击元素，后者是绑定事件的元素



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



子组件可以直接接受父组件传入的方法，子组件不必通过emit 触发父组件方法，直接执行即可。

相当于在子组件中直接调用父组件中定义的方法。



类型检查

```react
class List extends React.Component {}
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

什么是不可变值

当我们修改state的值，不要直接操作 state

需要用一个变量，把state赋值给 这个变量，这个变量改变后，在通过 setState 修改。

我们在对 state 某个值赋值的时候，不能影响之前 state的值，比如 arr 的push操作，会影响原数据，不行。

一句话，在setState 之前，不能修改state值。

注意，不能直接对 this.state.list 进行 push pop splice 等，这样违反不可变值

```react
arr.concat(100)   //arr 值没有改变

arr.push(100)  //arr 值改变
```

####  7-9 setState是同步还是异步 (07:01)

可以理解为类似 ajax， 是异步操作，我们

在 setState 后，访问state 值，是没有修改过的state值

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

bodyClickHandler = () => {
  this.setState({
    count: this.state.count + 1
  })
  console.log('count in body event', this.state.count)
}
componentDidMount() {
  // 自己定义的 DOM 事件，setState 是同步的
  console.log('lala')
  document.body.addEventListener('click', this.bodyClickHandler)
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

   

####  7-14 什么是React非受控组件 (09:18)

####  7-15 什么场景需要用React Portals (05:37)

####  7-16 是否用过React Context (12:22)

####  7-17 React如何异步加载组件 (07:33)

#### 7-18 React性能优化-SCU的核心问题在哪里 (06:55)

####  7-19 React性能优化-SCU默认返回什么 (08:51)

####  7-20 React性能优化-SCU一定要配合不可变值 (09:17)

####  7-21 React性能优化-PureComponent和memo (03:13)

####  7-22 React性能优化-了解immutable.js (03:52)

####  7-23 什么是React高阶组件 (12:31)

####  7-24 什么是React Render Props (08:55)

####  7-25 React高级特性考点总结 (02:24)

####  7-26 Redux考点串讲 (03:39)

####  7-27 描述Redux单项数据流 (03:22)

#### 7-28 串讲react-redux知识点 (05:14)

####  7-29 Redux action如何处理异步 (03:32)

####  7-30 简述Redux中间件原理 (07:07)

####  7-31 串讲react-router知识点 (04:02)

####  7-32 React使用-考点总结 (10:29)