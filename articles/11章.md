## 11-1 独立负责项目能力

一般二面，三面，会有场景题。

视图：组件结构和拆分

状态： 数据结构设计

数据驱动视图

1. react 设置一个todolist

2. vue 设计一个购物车


## 11-2、3、4、5 略

## 11-6、7 购物车原型图

购物车原型图

[!img]()


## 11-8  购物车设计


注意代码中子父组件通信：

```
	$event.$on
	$event.$emit
	
	不需要在父组件中注册方法，
	
	父组件：
	<CartList
        :productionList="productionList"
        :cartList="cartList"
    />
        
	mounted() {
        event.$on('addToCart', this.addToCart)
        event.$on('delFromCart', this.delFromCart)
    }
    
    子组件：
    clickHandler(id, e) {
        e.preventDefault()
        event.$emit('addToCart', id)
    }
```




## 11-9 使用vuex 设计购物车








