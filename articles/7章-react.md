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

####  





####  7-6 React表单知识点串讲 (07:57)

####  7-7 React父子组件通讯 (08:41)

####  7-8 setState为何使用不可变值 (14:44)

####  7-9 setState是同步还是异步 (07:01)

####  7-10 setState合适会合并state (07:22)

####  7-11 React组件生命周期 (05:49)

####  7-12 React基本使用-知识点总结和复习 (02:50)

####  7-13 React函数组件和class组件有何区别 (06:36)

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