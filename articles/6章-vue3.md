## 6-1

vue3 升级内容

1. 全部使用ts重写 
2. 提升性能，代码量减少，打包后代码量减少
3. 调整部分api

 proxy 存在浏览器兼容性问题，且不能使用 polyfill
 
 
## 6-2 proxy基本使用-1
 
 Object.defineProperty 缺点：
 	
 	深度监听需要一次性递归
 	无法监听新增属性/删除属性 Vue.set Vue.delete
 	无法原生监听数组，需要特殊处理
 	
proxy 实现响应式
	
	在 set 中监听到新增属性
	在 deleteProperty 中监听到删除属性
	支持监听数组的变化，不需要单独对数组进行处理

基本语法

```
var data =

var proxyData = new Proxy(data, {
	get(),
	set(),
	deleteProperty()

})
```
## 6-3 proxy基本使用-2

```
//所以receiver指向proxy对象 = proxyData.

const data = ['a', 'b', 'c']

const proxyData = new Proxy(data, {
    get(target, key, receiver) {
        // 只处理本身（非原型的）属性
        const ownKeys = Reflect.ownKeys(target)
        if (ownKeys.includes(key)) {
            console.log('get', key) // 监听
        }

        const result = Reflect.get(target, key, receiver)
        return result // 返回结果
    },
    set(target, key, val, receiver) {
        // 重复的数据，不处理
        if (val === target[key]) {
            return true
        }

        const result = Reflect.set(target, key, val, receiver)
        console.log('set', key, val)
        // console.log('result', result) // true
        return result // 是否设置成功
    },
    deleteProperty(target, key) {
        const result = Reflect.deleteProperty(target, key)
        console.log('delete property', key)
        // console.log('result', result) // true
        return result // 是否删除成功
    }
})
```


对数组的监听：
	
	const data = ['a', 'b', 'c']
	proxyData.push('d') 
	
	会打印出来：
	get length
	set 3 d
	
	proxyData.ownkeys: 0, 1, 2, length  
	注意，length 也是 proxyData 的一个熟悉。
	
	proxyData[0]= 'aa';
	会触发：  
	set 0 aa  //表示可以通过数组长度设置数组值
	
	proxyData[3] = 'cc';
	set 3 cc 
 

## 6-4 vue3用Proxy实现响应式




1. 深度监听

		在 get返回时
		return reactive(result)
	 
2. 区别是修改原有的属性，还是新增属性
	
		if (ownKeys.includes(key)) 



```JavaScript
function reactive(target = {}) {
    if (typeof target !== 'object' || target == null) {
        // 不是对象或数组，则返回
        return target
    }

    // 代理配置
    const proxyConf = {
        get(target, key, receiver) {
            // 只处理本身（非原型的）属性
            const ownKeys = Reflect.ownKeys(target)
            if (ownKeys.includes(key)) {
                console.log('get', key) // 监听
            }
    
            const result = Reflect.get(target, key, receiver)
        
            // 深度监听
            // 性能如何提升的？
            return reactive(result)
        },
        set(target, key, val, receiver) {
            // 重复的数据，不处理
            if (val === target[key]) {
                return true
            }
    
            const ownKeys = Reflect.ownKeys(target)
            if (ownKeys.includes(key)) {
                console.log('已有的 key', key)
            } else {
                console.log('新增的 key', key)
            }

            const result = Reflect.set(target, key, val, receiver)
            console.log('set', key, val)
            // console.log('result', result) // true
            return result // 是否设置成功
        },
        deleteProperty(target, key) {
            const result = Reflect.deleteProperty(target, key)
            console.log('delete property', key)
            // console.log('result', result) // true
            return result // 是否删除成功
        }
    }

    // 生成代理对象
    const observed = new Proxy(target, proxyConf)
    return observed
}
```








