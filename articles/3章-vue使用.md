1章、2章 略

## review

deep, 为什么不能打印出old值？  
组件间通讯 - 自定义事件  
父子组件销毁顺序？  


## 3-1 

差不多1个小时之内，面试时间是有限的，最长也就一个半小时

## 3-2 

1. v-html: 把数据完全用 html 语法展示出来

2. computed 和 method区别

		computed 有缓存，data 不变不会重新计算

3. watch 如何深度监听？

当属性变化时候，可以监听到的意思，如果不加 deep:true 如何？

当对象不添加， deep: true 的时候，属性变化是监听不到的
	
		deep: true
		handler(oldval, val) {}

<<<<<<< HEAD
	### watch 如果监听引用类型，是拿不到 oldVal 的 ？？？ 这是什么意思
	
	```
=======
### watch 如果监听引用类型，是拿不到 oldVal 的 ？？？ 这是什么意思

```js
>>>>>>> 9d7f20c85eafaf83fab30e68a3e67d217d72e350
	var vm = new Vue({
        el: '#app',
        data: {
            name: '双越',
            info: {
                city: '北京'
            }
        },
        watch: {
            name(oldVal, val) {
                console.log('watch name', oldVal, val) // 值类型，可正常拿到 oldVal 和 val
            },
            info: {
                handler(oldVal, val) {
                    console.log('watch info', oldVal, val)
                    console.log('watch info', oldVal.city, val.city)  // 显示一样

                  // 引用类型，拿不到 oldVal 。因为指针相同，此时已经指向了新的 val
                },
                deep: true // 深度监听
            }
        }
    })
```

怎么改：

可以改成：
	'info.city': {}

或者可以通过 计算属性	

	computed: {
	    getName: function() {
	    	return this.info.name
	    }
	}	


​	
4. 动态绑定 class 的几种写法

	```
	<p :class="{ black: isBlack, yellow: isYellow }">使用 class</p>
	<p :class="[black, yellow]">使用 class （数组）</p>
	<p :style="styleData">使用 style</p>
	
	```

5. v-if v-show 区别

		v-if 表达式为false, 没有匹配， dom 中就没有
		v-show style="display: none"  但还是在dom 中有
	
	使用场景？
		
		一次性使用的 v-if
		频繁切换，v-show, 使用 display 切换显示和隐藏
	
	
## 3-3  基本串讲

event 参数，自定义参数  
	
	@click="incre(1, $event)"
	
	increment1(value, event) {
	    console.log('event', event, event.__proto__.constructor) // 是原生的 event 对象
	
	    console.log(event.target)  //实际点击的元素，始终指向事件发生时的元素
	
	    console.log(event.currentTarget) // 注意，事件是被注册到当前元素的，和 React 不一样
	    //绑定方法的元素，代理。指向事件所绑定的元素
	
	    this.num++
	
	    // 1. event 是原生的
	    // 2. 事件被挂载到当前元素
	    // 和 DOM 事件一样
	}


​	
​	
	event.__proto__.constructor  是原生的 event 对象
	
	event.target  
	
	event.currentTarget 




事件修饰符，按键修饰符  

	v-on:click.stop   阻止事件冒泡
	v-on:submit.prevent 阻止默认行为
<<<<<<< HEAD
=======
	v-on:click.capture 内部元素触发事件先在此处理，然后才交给内部元素进行处理 
>>>>>>> 9d7f20c85eafaf83fab30e68a3e67d217d72e350

事件被绑定到哪里


## 3-4 父子通信、自定义事件

#### 1. props 和 ```$emit```

```javascript

子组件接受数据，传递数据
props: {
    // prop 类型和默认值
    list: {
        type: Array,
        default() {
            return []
        }
    }
},

methods: {
    deleteItem(id) {
        this.$emit('delete', id)
    },
}

父组件中，注册事件
<List :list="list" @delete="deleteHandler"/>
methods: {
    deleteHandler(id) {
        this.list = this.list.filter(item => item.id !== id)
    }
},
```



#### 2.组件间通讯 - 自定义事件




```
主要用于，兄弟、亲戚 组件之间通信
	
//定义event.js
import Vue from 'vue'
export default new Vue()

	
// A 页面触发自定义事件
import event from './event'
event.$emit('onAddTitle', this.title)
   	
   	
// B 页面，注册监听
import event from './event'
mounted() {
    // 绑定自定义事件， 这里绑定，事件名称，解绑自定义事件。
    event.$on('onAddTitle', this.addTitleHandler)
},
beforeDestroy() {
    // 及时销毁，否则可能造成内存泄露
    event.$off('onAddTitle', this.addTitleHandler)
}
```
<<<<<<< HEAD
=======

#### vue中利用 bus 做组件间传值

有两种方法，一种是在每个使用该页面的地方，都 导入事件对象

另外一种是在main.js 中挂到 prototype上

```js
import Vue from 'vue'
export default new Vue()

import event from './event'

let bus = import bus from './event'
Vue.prototype.$bus = bus
```
>>>>>>> 9d7f20c85eafaf83fab30e68a3e67d217d72e350



## 3-6 父子组件生命周期

![img](https://segmentfault.com/img/bVbq6SZ)


1. beforeCreate，实例初始化在这个生命周期遍历 data 对象下所有属性将其转化为 getter/setter,  无data, 
2. created，实例已经被创建完毕 属性已经绑定 属性是可以操作的, 有data。在控制台打印data ，可以访问到属性了。**Vue实例初始化完毕，但是还没开始渲染。**
3. beforeMount， 模板编译， el还未对数据进行渲染 还是不能操作dom，这个时候不能访问 `$ref, $el` 原生dom

4. mounted, **页面渲染完**，数据挂载到 dom 上  ，控制台打印出dom。	**对象页面渲染完毕，可以ajax请求，或者绑定事件**	

5. beforeUpdate， **data被修改，dom没有修改**	

6. updated， **虚拟dom重新渲染并应用更新**	

7. beforeDestroy，销毁绑定事件监听	

 	beforeDestroy钩子函数在实例销毁之前调用。在这一步，实例仍然完全可用。 	

 	比如消除 setTimout 定时任务，绑定事件

8. destroyed钩子函数在Vue 实例销毁后调用。调用后，Vue 实例指示的所有东西都会解绑定，所有的事件监听器会被移除，所有的子实例也会被销毁。



**创建是从外到内的，但是渲染时从内到外的**，子组件渲染完，父组件才渲染完毕




<<<<<<< HEAD

=======
>>>>>>> 9d7f20c85eafaf83fab30e68a3e67d217d72e350
父 beforeCreate > 父created > 父 beforMount , 子组件beforeCreate ， 子组件created > 子组件beforMount， 子组件mounted，  父组件 mounted


父组件 beforeupdate > 子组件beforeupdate > 子组件 updated> 父组件 updated






<<<<<<< HEAD
​	
=======
>>>>>>> 9d7f20c85eafaf83fab30e68a3e67d217d72e350
## 3-7 略
## 3-8 v-model

一个组件上的 v-model 默认会利用名为 value 的 prop 和名为 input 的事件

```
<CustomVModel v-model="name"/>

<template>
    <!-- 例如：vue 颜色选择 -->
    <input type="text"
        :value="text1"
        @input="$emit('change1', $event.target.value)"
    >
    <!--
        1. 上面的 input 使用了 :value 而不是 v-model
        2. 上面的 change1 和 model.event1 要对应起来
        3. text1 属性对应起来
    -->
</template>

<script>
export default {
    model: {
        prop: 'text1', // 对应 props text1
        event: 'change1'
    },
    props: {
        text1: String,
        default() {
            return ''
        }
    }
}
</script>
```

注意点3：
	
	1. 组件中的 input不能使用 v-model,使用的是 v-bind
	2. model 中的prop 和 props 对应起来
	3. event 和 emit 中 对应

## 3-9 nextTick


首先Vue 是异步渲染的框架

**data 改变后，dom不会立刻渲染**，如果此时你想拿到dom的一些属性，对dom的一些操作，或者数据，不是更新后的、实时的数据，如果我们想获取dom更新后的数据，需要在 nextTick 中获取（这里注意，是对dom的操作才会有！）

**nextTick 会在 Dom 更新之后被触发，以获取最新的 DOM 节点**



**1.nextTick 是在 dom 渲染之后完成的。2. 涉及对DOM 的操作**

```
1. 异步渲染，$nextTick 待 DOM 渲染完再回调
2. 页面渲染时会将 data 的修改做整合，多次 data 修改只会修改渲染一次

addItem() {
        this.list.push(`${Date.now()}`)
        this.list.push(`${Date.now()}`)
        this.list.push(`${Date.now()}`)

        // 1. 异步渲染，$nextTick 待 DOM 渲染完再回调
        // 3. 页面渲染时会将 data 的修改做整合，多次 data 修改只会渲染一次
        this.$nextTick(() => {
          // 获取 DOM 元素
          const ulElem = this.$refs.ul1
          console.log( ulElem.childNodes.length )
        })
    }
```

## 3-10 slot

具名插槽   
匿名插槽  
作用域插槽  

如果 <navigation-link> 没有包含一个 <slot> 元素，则该组件起始标签和结束标签之间的任何内容都会被抛弃。

注意 v-slot 只能添加在 ```<template>``` 上 (只有一种例外情况)，这一点和已经废弃的 slot attribute不同。

```
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
</div>

<base-layout>
  <template v-slot:header>
    <h1>Here might be a page title</h1>
  </template>
</base-layout>


// 自 2.6.0 起被废弃
<template slot="header">
    <h1>Here might be a page title</h1>
  </template>

```

## 3-11 动态组件


```
:is = 'component-name' 
<component v-bind:is="currentTabComponent"></component> 
```
根据数据，动态渲染的场景，即组件内容不确定  


## 3-12 vue 异步组件

import 函数  
按需组件  

如果按照 ,在文件头部引入文件，是同步加载组件

```JavaScript
<script>
import a from ''   // 同步引入组件，打包成一个文件

export default {

}
...

```
动态引入组件：

```
components: {
	FormDemo: ()=> import ('../components/FormDemo')
}
```

1. webpack把异步引入的组件单独划分成一个js文件
2. import是运行时加载，什么时候运行到这一句，就会加载指定的模块。使用动态的方式引入, 什么时候用什么时候加载，什么时候不用，永远不会加载





## 3-13 keep-alive

作用：缓存组件，当我们频繁的切换组件，不需要重复渲染	
vue 常见性能优化



使用 v-if 显示组件，组件 是会销毁和重新渲染组件, 显示触发 mounted，v-if: false 时，会触发 destroyed

```
<keepaliveStageA v-if="state === A"/>
```

改成：

```
<keep-alive>
	<keepaliveStageA v-if="state === A"/>
</keep-alive>
```

和 v-show 有什么区别？

v-show 是通过  display 控制的，是通过css控制			
keep-alive 是把组件这个对象缓存起来，一个组件也是一个对象


## 3-14 mixin


多个组件有相同的逻辑，抽离出来

和 小程序的 behaviors 一样


### (面试问到了
值为对象的选项，例如 methods、components 和 directives，将被合并为同一个对象。两个对象键名冲突时，取组件对象的键值对。

methods 和 data 中，相同的方法或者属性名可能会冲突，组件中会代替， mixin 中定义的属性或者方法。	

而 mounted 中，不会覆盖, 会合并



```
// myMixin.js
export default {
	data(){
	},
	methods:{
	},
}

import myMixin from './myMixin'
import myMixin2 from './myMixin2'


export default {
	mixins: [myMixin, myMixin2],
	data(){
	},
	methods:{
	},
}

```



mixin 的问题：

1. 变量来源不明（需要在 mixin 文件中找到），不利于阅读
2. 多个 mixin 可能造成命名冲突， 后面定义的会覆盖前面定义的
3.  mixin 和组件可能出现多对多的关系，复杂度较高。一个组件可以引入多个 mixin, 一个mixin 可以被多个组件引入。


## 3-15 面试技巧

可以不太深入，但必须知道

熟悉基本用法，了解使用场景

最好能和自己的项目经验结合起来（应用和自己的生产经验结合起来

## 3-16 vuex

state 

getters

actions  才能做异步操作

mutation 

dispatch 

commit 



## 3-17 vue-router


路由模式: hash 、 h5 history   
路由配置: 动态路由、懒加载

hash 默认： http://abc.com/#/user/10  
h5 history 模式: http://abc.com/user/20  

第二种，需要server 端支持，因此无特殊需求可选择前者



正常服务器返回：根据请求不同路径名，返回不同的 html 文件

比如，访问 abc.com 返回 abc.html  
访问 abc.com/user  返回 **/user.html  
h5 ，无论访问什么路径，都返回 index.html  

```javascript
{
  "hosting": {
    "public": "dist",
    "rewrites": [
      {
        "source": "**",
        "destination": "/index.html"
      }
    ]
  }
}
```
所以需要配置 404 页面

给个警告，因为这么做以后，你的服务器就不再返回 404 错误页面，因为对于所有路径都会返回 index.html 文件。为了避免这种情况，你应该在 Vue 应用里面覆盖所有的路由情况，然后在给出一个 404 页面。

```javascript
const router = new VueRouter({
  mode: 'history',
  routes: [
    { path: '*', component: NotFoundComponent }
  ]
})
```

或者，如果你使用 Node.js 服务器，你可以用服务端路由匹配到来的 URL，并在没有匹配到路由的时候返回 404，以实现回退。

动态路由：

```
//动态路径参数，以冒号开头，能命中 '/user/10', '/user/20' 等格式的路由

{ path: 'user/:id',  component: User }
```

路由懒加载：

```
routes: [
	{
		path: '/a',
		component: ()=> import('../component/a')
	}
]
```


