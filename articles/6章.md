## 6-1

vue3 升级内容

1. 全部使用ts重写 
2. 提升性能，代码量减少，打包后代码量减少
3. 调整部分api

 proxy 存在浏览器兼容性问题，且不能使用 polyfill
 
 
## 6-2 、6-3
 
proxy 实现响应式

get、set 中监听不到新增属性。
可以监听到新增属性

基本语法

```
var data =

var proxyData = new Proxy(data, {
	get(),
	set(),
	delete()

})
```


```
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



## 6-4












