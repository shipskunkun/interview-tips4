## 4-1 vue原理

原理：
	
	组件化MVVM
	响应式
	vdom,diff
	模板编译
	组件渲染过程
	前端路由

## 4-2 组件化基础

很久之前就有组件化

组件化将页面视为一个容器，页面上各个独立部分例如：头部、导航、焦点图、侧边栏、底部等视为独立组件，不同的页面根据内容的需要，去盛放相关组件即可组成完整的页面。

组件是具体的：按照一些小功能的通用性和可复用性来抽象组件
组件化更多的关注UI部分，比如用户看到的弹出框，页脚，确认按钮等，这些组件可以组合成新的组件，又可以和其他组件组合组合成新的组件

模块是抽象的：按照项目业务划分的大模块
模块化侧重于数据的封装，一组相关的组件定义成一个模块，一个json对象可以是一个模块。模块之间有依赖的关系。import，AMD，CMD，commonjs





asp、jsp、php 已经有组件化了

传统组件，只是静态渲染，更新还需要依赖于操作 DOM   
数据驱动视图，vue MVVM  
数据驱动视图，react setState  

![img](https://ss2.bdstatic.com/70cFvnSh_Q1YnxGkpoWK1HF6hhy/it/u=2307375690,2929922670&fm=26&gp=0.jpg)

## 4-3、4-4 数据监听

如何起一个服务？  
在目标文件夹下：  



```js
cnpm i http-server -g
http-server -p 8001
```



#### 区别，面试问到了

oninput和onchange的区别

不同之处在于oninput事件在元素值发送变化是立即触发，onchange在元素失去焦点时触发





```
npm i http-server -g   
http-server -p 8001  

// updateView 可能这么写？
function observe() {
    spanName.innerHTML = obj.name;
    inpName.value = obj.name;
}
inpName.oninput = function () {
    obj.name = this.value;
};




// 一级监听
function defineReactive(target, key, value) {
    // 核心 API
    Object.defineProperty(target, key, {
        get() {
            return value
        },
        set(newValue) {
            if (newValue !== value) {
                // 设置新值
                // 注意，value 一直在闭包中，此处设置完之后，再 get 时也是会获取最新的值
                value = newValue

                // 触发更新视图
                updateView()
            }
        }
    })
}

```
思路：
定义函数 observer：

	1. 判断是否是对象还是基础类型
	2. 判断是否是数组
	3. 对每个obeject每个key监听
监听函数：

	1. 对value 执行 observer 函数
	2. 使用Object.defineProperty 

```

// 深度监听
function defineReactive(target, key, value) {
    // 深度监听
    observer(value)

    // 核心 API
    Object.defineProperty(target, key, {
        get() {
            return value
        },
        set(newValue) {
            if (newValue !== value) {
                // 深度监听
                observer(newValue)

                // 设置新值
                // 注意，value 一直在闭包中，此处设置完之后，再 get 时也是会获取最新的值
                value = newValue

                // 触发更新视图
                updateView()
            }
        }
    })
}



function observer(target) {
    if (typeof target !== 'object' || target === null) {
        // 不是对象或数组
        
        return target
    }
   
    // 重新定义各个属性（for in 也可以遍历数组）
    for (let key in target) {
        defineReactive(target, key, target[key])
    }
}


// 准备数据
const data = {
    name: 'zhangsan',
    age: 20,
    info: {
        address: '北京' // 需要深度监听
    },
    nums: [10, 20, 30]
}

// 监听数据
observer(data)

data.name = 'lisi'
data.x = '100' // 新增属性，监听不到 —— 所以有 Vue.set
delete data.name // 删除属性，监听不到 —— 所有已 
data.info.address = '上海' // 深度监听

```

深度监听和 普通监听的区别：  
变化，在 设置新值 和 对每个属性 value 进行 observer 函数

如果监听的属性值不是对象，return，否则，继续对这个属性深度监听。


Obect.defineProperty   的缺点：
	
	1. 深度监听，需要递归，一次性计算量大
	监听对象的属性，如果属性还是对象，需要深度监听，而且是递归
	
	2. 无法监听新增、删除属性
		无法监听 data.x 添加属性， delete data.name 删除属性
		如果data 添加了新的属性，是无法监听到的，需要使用 Vue.set
		无法监听 delete data 中的属性, Vue.delete
	
	3. 无法原生监听数组，需要特殊处理
		既需要继承原生数据的特性，而且还能不修改原生数组，还需要监听
		数据原生的 push、pop、shift、unshift 等这些方法，因为要触发 re-render
		所以让新的数组的 __proto__ = Obeject.create(Array.prototype)


​	
## 4-5 监听数组变化

 	需要对监听的数组，做深拷贝
 	
 	为啥要这么做，可以监听，数组的 push、pop 等方法，对数组的修改，更新视图
 	
 	
```
 function observer(target) {
    if (typeof target !== 'object' || target === null) {
        // 不是对象或数组
        return target
    }

   	// 如果是数组的话
    if (Array.isArray(target)) {
        target.__proto__ = arrProto
    }

    // 重新定义各个属性（for in 也可以遍历数组）
    for (let key in target) {
        defineReactive(target, key, target[key])
    }
} 	

 	
 	// arrProto 类的数组，有啥特点
 	const oldArrayProperty = Array.prototype
 	
	// 创建新对象，原型指向 oldArrayProperty ，再扩展新的方法不会影响原型
	
	const arrProto = Object.create(oldArrayProperty);
	
	['push', 'pop', 'shift', 'unshift', 'splice'].forEach(methodName => {
    arrProto[methodName] = function () {
        updateView() // 触发视图更新
        oldArrayProperty[methodName].call(this, ...arguments)
        
        // Array.prototype.push.call(this, ...arguments)
        
    }
})
```


## 4-6、4-7 虚拟dom, virtual dom


dom操作非常消耗性能  
以前用jQuery，可以自行控制Dom 操作的时机，手动调整  
vue 和 react 是数据驱动视图，如何有效控制dom 操作？  


把对dom的操作转移为对js的计算，因为js执行速度很快  
使用js模拟dom结构，计算出最小的变更，操作dom  

使用js对象模拟dom结构

```
{
tag: 'div',
props: {
	className: 'container',
	id: 'div1'
}
children: [{
		tag: 'p',
		children: 'vdom'
	},
	{
		tag: 'ul',
		props: {style: 'font-size: 20px'},
		children: [
			{
				tag: 'li',
				children: 'a'
			}
		]
	}
]
}
```

## 4-8 diff 算法

树的diff 时间复杂度是 O(n^3)
	

为什么O(n^3),  编辑距离的时间复杂度。



	遍历tree1, 
	遍历tree2
	排序

 算法如何优化到O(n)
 	
 	1. 只比较同一层级，不跨级比较
 	2. tag不相同，则直接删掉重建，不再深度比较
 	3. tag 和 key, 两者都相同，则认为是相同节点，不再深度比较

	
## 深
## 4-9、深入 diff 算法

* @param sel    选择器
	
		h('div#container.two,classes', {})
		div#container.two,classes 就是 sel

* @param data    绑定的数据

* @param children    子节点数组

* @param text    当前text节点内容

* @param elm    对真实dom element的引用


``` 
//sel: string || undefined  这是什么东西？？？
//key: data.key || undefined
//elm: element || text || undfined, 渲染到哪个Element上去
// children 和 text 不能共存，子元素要么是文本，要么是 有很多子节点的 节点

var vnode = h(sel, data, children, text, elm, key)

var node = h('div#container.two.classes', {on: {click: someFn}},
[h(), 'text', h()])


patch(container, vnode)

var newVnode = h()
patch(vnode, newVnode)
```

## 4-10


判断两个节点是否相等

```
function  sameFunction(vnode1, vnode2) {
	return vnode1.key === vnode2.key && vnode1.sel === vnode2.sel
}
```
## 4-11  patchNode 函数

patch(vnode, newVnode)

分几种情况

if(oldVnode === vnode) return


问题： 
	没有文本，就是一定有孩子么？是的。

![img](https://images2018.cnblogs.com/blog/998023/201805/998023-20180519212357826-1474719173.png)


1. 部分代码

		if(isUndef(vnode.text))   // 如果新的节点 不是文本节点 。
	
		意味着，新节点，要么是空，要么有孩子，
		//如果新旧节点都有孩子
		if(isDef(oldCh) && idDef(ch))  
		// 两孩子不相等，比较孩子节点
			if( oldCh !== ch)  updataChildren(...,...)  
			 else if (isDef(ch))  // 如果新节点有孩子，旧节点没孩子
			if(isDef(oldVnode.text) api.setTextContent() // 如果新节点孩子有文本，添加文本节点 
			addVnodes()  // 添加，新节点孩子为 Vnode
		else if( idDef(oldCh))  //如果旧结点有孩子， 新节点无孩子
				removeNodes(...,...)  // 把旧结点孩子移除
		else if( isDef(oldVnode.text)) {  //如果旧节点的无孩子，但是节点有本
			api.setTextContent(elm, '') //旧节点文本置为
		else if(oldeVonde.text !== vnode.text)   // 如果新旧节点 text 不一致
		if(isDef(oldCh)) {  // 如果旧节点还有孩子
			removeVondes(elm, oldCh, ...)  // 移除旧节点 孩子
		}
		api.setTextContent(elm, vnode.text)  // 把新旧节点，text 设成一样
	


 核心patch:
 	
	 	两个都有孩子，比较孩子
	 	如果新的有孩子，旧的没有，直接添加
	 	如果新的没有孩子，旧的有，移除


## 4-12 updataChildren  （ todo




假如在同一层级的：

oldCh = a b c d	 
newCh = b e d c 

有增加、删除、排序的变化

## 4-13 vdom 和 diff 总结

patchVonde    
addVnodes removeVnodes    
updataChildren(key) 的重要性  



## 4-14 模板编译1

模板是 vue 开发中最常用的部分，即与使用相关联的原理

它不是 html， 有指令、差值、js表达式，到底是什么？


前置知识： js的with 语法

1. vue template complier 将模板编译为 render 函数
2. 执行 render 函数，生成 vnode


with 语法

+ 改变{} 内自由变量的查找规则，当做 obj 的属性来查找
+ 如果找不到匹配的 obj 的属性，就会报错
+ 慎用，打破作用域规则，易读性变差

```
var  obj = {a:1, b:2}
with(obj) {
	console.log(a);
	console.log(b);
}
```


## 4-15 模板编译2

+ 模板不是html， 有指令、差值、JS表达式，能实现判断、循环
+ html是标签语言，只有JS 才能实现判断、循环
+ 模板一定是转换成某种 JS 代码，这个过程叫模板编译

```
const res = compiler.compile(template)
// 打印出 JS 代码

this: vm的实例
返回的 render 函数

with(this){return _c('p', [_v(_s(message))] )}

_c  createElement
_v  createVnode
_s  toString
```

compiler 将模板编译成 render 函数

执行 返回的 render 函数， 得到 vnode

基于 vnode 再执行patch 和 diff

vue-loader ，在开发环境下，执行编译模板


```
//表达式
const template = `<p>{{flag ? message : 'no message found'}}</p>`

with(this){return _c('p',[_v(_s(flag ? message : 'no message found'))])}
```

``` JavaScript
//属性和动态属性
const template = `
     <div id="div1" class="container">
         <img :src="imgUrl"/>
     </div>
 `
 with(this){return _c('div',
      {staticClass:"container",attrs:{"id":"div1"}},
      [
          _c('img',{attrs:{"src":imgUrl}})])}

attrs:
_c:  createElement:

```


```
// 条件
 const template = `
     <div>
         <p v-if="flag === 'a'">A</p>
         <p v-else>B</p>
     </div>
 `
 with(this){return _c('div',[(flag === 'a')?_c('p',[_v("A")]):_c('p',[_v("B")])])}
```

```
// 循环
 const template = `
     <ul>
         <li v-for="item in list" :key="item.id">{{item.title}}</li>
     </ul>
 `
 with(this){return _c('ul',_l((list),function(item){return _c('li',{key:item.id},[_v(_s(item.title))])}),0)}

_l:   生成 li

```

```
// 事件
 const template = `
     <button @click="clickHandler">submit</button>
 `
 with(this){return _c('button',{on:{"click":clickHandler}},[_v("submit")])}
 
on:  绑定事件
```

```
// v-model
const template = `<input type="text" v-model="name">`
 主要看 input 事件
 
 with(this){return _c('input',{directives:[{name:"model",rawName:"v-model",value:(name),expression:"name"}],attrs:{"type":"text"},domProps:{"value":(name)},on:{"input":function($event){if($event.target.composing)return;name=$event.target.value}}})}

```


## 4-16 render 代替 template

```
Vue.component('heading', {
	
	// tempalte: `**`
	render: function(){}   //可以使用 render 代替 template

})
```

再某些复杂的情况下，不能用 remplate, 可以用 render

react 一直使用 render， 没有模板

## 4-17  review
## 4-18 vue 如何渲染和更新的


####问道了！

模板中没有使用的属性，不会被收集依赖。

初次渲染：
	
	1. 解析模板为 render 函数, 生成虚拟dom
	2. 触发响应式，监听 data 属性  getter、setter


		通过 with 语法，已经获得了 data 的属性，触发 getter
		前提：在template 中出现的 属性，才会触发 getter
	
	3. 执行 render 函数，生成 vnode， patch(elem， vnode)

更新过程：
	
	1. 修改data , 触发 setter
	2. 重新执行render 函数，生成 newVode
			
			重点！setter 中
			
	3. patch(vnode, newVnode)


流程图

从黄色的框开始看。

![img](https://cn.vuejs.org/images/data.png)

## 4-19 vue 组件异步渲染

回顾 $nextTick

1. $nextTick 等 DOM 渲染完再回调
2. 页面渲染时会将 data 的修改做调整，多次 data 修改只会渲染一次


3. 汇总 data 修改一次性更新视图
4. 减少 DOM 操作次数，提高性能


## 4-20 js 实现 hash

稍微复杂一点的SPA， 都需要路由

vue-router 也是 vue 全家桶的标配之一

```
http://127.0.0.1:8881/01-hash.html?a=100&b=20#/aaa/bbb

location.protocol    http:
location.hostname     127.0.0.1
location.host			127.0.0.1:8881
location.port			8881
location.pathname	/01-hash.html
location.search		?a=100&b=20
location.hash			#/aaa/bbb

```

hash 的特点：

1. hash 的变化会触发网页跳转，即浏览器的前进后退
2. hash变化不会刷新页面，SPA必须的特点
3. hash 永远不会提交到 server 端

```
// hash 变化，包括：
// a. JS 修改 url
// b. 手动修改 url 的 hash
// c. 浏览器前进、后退
window.onhashchange = (event) => {
    console.log('old url', event.oldURL)
    console.log('new url', event.newURL)

    console.log('hash:', location.hash)
}

// 页面初次加载，获取 hash
document.addEventListener('DOMContentLoaded', () => {
    console.log('hash:', location.hash)
})

// JS 修改 url
document.getElementById('btn1').addEventListener('click', () => {
    location.hash = '#/user'

    console.log("href", location.href);
    console.log("hash", location.href);
})
```

## 4-21 js 实现 h5 history

用URL 规范的路由，但跳转时不刷新页面

history.pushState

window.onpopstate

表现：

http://a.com/xx

跳转到
http://a.com/xx/yy 不刷新页面



1. 使用 putState 方式跳转，页面不会刷新, h5  history.pushState无刷新改变url
2. 当用户在浏览器点击进行后退、前进，或者在js中调用histroy.back()，history.go()，history.forward()等，会触发popstate事件；但pushState、replaceState不会触发这个事件。



```
 // 页面初次加载，获取 path
document.addEventListener('DOMContentLoaded', () => {
    console.log('load', location.pathname)
})

// 打开一个新的路由
// 【注意】用 pushState 方式，浏览器不会刷新页面
document.getElementById('btn1').addEventListener('click', () => {
    const state = { name: 'page1' }
    console.log('切换路由到', 'page1')
    history.pushState(state, '', 'page1') // 重要！！
})

// 监听浏览器前进、后退
window.onpopstate = (event) => { // 重要！！
    console.log('onpopstate', event.state, location.pathname)
}
```

需要后端配合：

无论你访问什么路由，都返回  index.html 页面   
否则会报错， 跳转到新路由，这个页面找不到  



to B , 推荐使用 hash，简单易用，对url规范不敏感

to C， 考虑使用 h5 history, 但是需要服务端支持


## 4-22 review 


略
