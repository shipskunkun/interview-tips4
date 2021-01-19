#### 8-1 React原理-考点串讲 (06:04)

函数式编程

vdom 和 diff 算法

jsx本质

合成事件

setState batchUpdate

组件渲染过程

前端路由

#### 8-2 再次回顾不可变值 (03:46)

在修改state之前，不要修改state值

当state数据是数组时，注意对数组的操作是否影响原数组

最好的方式当然是用一个变量 = 原始state， state = 修改后的值



对象的时候，如何处理，Object.assign 或者扩展运算符 扩展属性。

#### 8-3 vdom和diff是实现React的核心技术 (06:20)

h函数，返回 vnode 数据解构

patch , 如何把 vnode 渲染到 dom 中？如何更新渲染？



diff算法，只比较同一层，不跨级比较

tag不同，直接删除重建，不再深度比较

tag 和 key 两者相同，认为是相同节点，不再深度比较



vue2 和 vue3 react 实现vdom 细节都不同

核心概念和实现思路都一样



#### 8-4 JSX本质是什么 (20:42)

jsx作用，等同于 vue模板

vue模板本质是js， 编译成函数，执行后生成 vnode

jsx 本质：

React.createElement 即 h 函数，返回vnode

第一参数，可能是组件，也可能是 html 标签

如何区分？组件第一个字母必须大写，看下面的实例，Input 就代表是组件。



面试时候：

函数是什么，返回结果是什么，函数参数



在babel官网，直接编译

官网： babeljs.cn

```react
const imgEle = <div id="div1">
                <p>some text</p>
                <img src={imgUrl}/>
              </div>
编译成：

"use strict";
// 第二个参数是 属性标签
//第三个参数，是通过递归调用 createElement 生成的
React.createElement('tag', null, child1, child2, child3)
const imgEle = React.createElement("div", {id: "div1"}, 
                                   React.createElement("p", null, "some text"), 			
                                   React.createElement("img", {src: imgUrl})
                                  );


const app = <div>
    <Input submitTitle={onSubmitTitle}/>
    <List list={list}/>
</div>
      
编译成：
// 不同于上面那个，这里的input是一个变量
const app = React.createElement("div", null, 
															React.createElement(Input, {submitTitle: onSubmitTitle}), 
															React.createElement(List, {  list: list}));


const listElem = <ul>{this.state.list.map((item, index) => {
    return <li key={item.id}>index {index}; title {item.title}</li>
})}</ul>

编译成：
const listElem = React.createElement("ul", null, 
(void 0).state.list.map((item, index) => {
return React.createElement("li", {key: item.id}, "index ", index, "; title ", item.title);
}));
```



#### 8-5 说一下React的合成事件机制 (08:54)

这种机制，就叫 合成事件机制。

所有事件挂载到 document上

event不是原生的，是合成事件对象

和vue事件不同，和 dom 事件也不同



	1. event 是 SyntheticEvent ，模拟出来 DOM 事件所有能力
    2. event.nativeEvent 是原生事件对象
    3. 所有的事件，都被挂载到 document 上
    4. 和 DOM 事件不一样，和 Vue 事件也不一样



为啥要做合成事件机制？

1. 为了兼容性和跨平台，迁移到移动端，改改直接用
2. 挂载到document 上，减少内存消耗，避免频繁解绑
   1. 没有挂载到组件上，挂载到document上。
3. 方便事件的统一管理，事务机制



#### 8-6 说一下React的batchUpdate机制 (10:41)

setState 和 batchUpdate 机制

就是 state 是同步还是异步？合并还是不合并？



setState 主流程

batchUpdate 机制

transaction 事务机制



```react
this.setState(new state)
newState 存入pending 队列
是否处于 batch update
是 异步更新
保存组件于 dirtyComponents中// state 已经被更新的组件 
否 同步更新
遍历所有的 dirtyComponents 调用 updateComponent 更新 pending state or props
```



回调函数式处于 batch update 中

setTimeout 不处于 batch update 中，为什么



setState 无所谓异步还是同步，看是否命中 isBatchingUpdates

具体看，在 setstate 的时候，isBatchingUpdates 这个值是true 还是false

如果是 false 同步更新，true 异步更新



1. 生命周期，调用的函数
2. react 中注册的世界
3. react 管理入口



#### 8-7 简述React事务机制 (02:52)

开始，处于batchUpdate

操作

结束，isbatchingUpdates false



#### 8-8 说一下React组件渲染和更新的过程 (06:47)

jsx 如何渲染到页面上的？

setState 如何更新页面？



JSX即 createElement函数

执行生成vnode

patch(elem, vnode) 和 patch(vnode, newVnode)



渲染过程：

根据 props 和 state, 通过render 生成 vnode, 最后挂载到 dom 上， patch(elem, vnode)



组件更新过程

setState  dirtyComponents （可能有子组件）

render 生成新 newVnode

patch（vnode， newVnode）

#### 8-9 React-fiber如何优化性能 (05:43)

patch 被拆分为两个阶段

1. 执行diff算法，纯js计算
2. commit阶段，将diff 结果渲染成dom



js单线程，且和 dom 渲染同一个线程

当组件足够复杂，组件的更新是计算和渲染压力较大

此时如果有dom更新需求（动画、鼠标拖拽），将卡顿



fiber 如何优化：

将第一阶段进行任务拆分，js计算过程拆分

dom渲染时暂停，空闲是恢复

如何知道dom需要时间？

window.requestIdleCallback



#### 8-10 React原理-考点总结和复习 (03:15)

