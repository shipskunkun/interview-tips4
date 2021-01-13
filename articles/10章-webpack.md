## 建议

不要背书，掉书袋，说说自己的理解

hot: true

	if(module.hot) {
		module.hot.accept(['./math'], ()=> {
		
		})
	}

10-24， 为什么要构建工具

## 10-1  webpack 常见面试题

常见面试题：  

	前端代码为何要构建和打包
	module chunk bundle 分别什么意思，有什么区别
	loader plugin 区别？
	webpack如何实现懒加载？  
	webpack常见性能优化  
	bebel-runtime 和 babel-polyfill 区别  


## 10-2、10-3 webpack 基本配置

```
webpack.common.js  公共配置
webpack.dev.js
webpack.prod.js

// webpack.dev.js 中
//通过merge ，与公共配置， 合并起来

const { smart } = require('webpack-merge')
module.exports = smart(webpackCommonConf, {
	mode: ,
	module:{
	},
	plugins: [
	]
})
```


启动本地服务：

	npm run dev
前端如何代理，解决跨域请求？
	
	proxy 代理，
	
	服务端给的接口是3000，本地的是8080，那么访问服务端的接口就跨域了，如何做？
	
	proxy: {
	    // 将本地 /api/xxx 代理到 localhost:3000/api/xxx
	    '/api': 'http://localhost:3000',
	
	    // 将本地 /api2/xxx 代理到 localhost:3000/xxx
	    '/api2': {
	        target: 'http://localhost:3000',
	        pathRewrite: {
	            '/api2': ''
	        }
	    }
	}

处理样式, loader 如果是多个的话，从后往前执行
	
	postcss-loader， 厂商前缀，浏览器兼容性
	css-loader，合并css文件；  
	style-loader，插入到html中，挂载到 header style 标签中
	
	{
		test: /\.css$/,
		loader:['style-loader', 'css-loader', 'postcss-loader']
	},{
		test: /\.less$/,
		loader:['style-loader','css-loader','less-loader']
	}

打包图片

	图片小于5kb， 打包到 img1 文件夹下，base64格式产出， 
	否则 依然沿用 file-loader , 打包后还是 dgsgsg.png 
		
	test: /\.(png|jpg|jpeg|gif)$/
	use: {
		loader: 'url-loader',
		options: {
			limit: 5 * 1024,
			outputPath: '/img1/'
		}
	}


​	
打包生成文件，如果打包的文件变了，hash值就会变，文件的缓存就会失效。	
如果js文件没变，请求的时候就会命中缓存。
​	
​	output: {
​		filename: 'bundle.[contentHash:8].js',
​		path: distPath
​	}
​	
	生成：
	
	bundle.12fjdggg.js
	img1
		gsgsgdgggsdshwrhhklbdhh.png


​	
​	
## 10-4 配置多入口


```
entry: {
    index: path.join(srcPath, 'index.js'),
    other: path.join(srcPath, 'other.js')
},


//production 中

output: {
    // filename: 'bundle.[contentHash:8].js',  // 打包代码时，加上 hash 戳
    filename: '[name].[contentHash:8].js', // name 即多入口时 entry 的 key
    path: distPath,
    // publicPath: 'http://cdn.abc.com'  // 修改所有静态文件 url 的前缀（如 cdn 域名），这里暂时用不到
},

生成了 index.html 和 other.html
我们需要 在 webpack.common.js 中，plugin中，给每一个页面做配置：
如果我们不写 chunks, 会把所有生成的 chunk.js 引入


plugins: [
    // 多入口 - 生成 index.html
    new HtmlWebpackPlugin({
        template: path.join(srcPath, 'index.html'),
        filename: 'index.html',
        // chunks 表示该页面要引用哪些 chunk （即上面的 index 和 other），默认全部引用
        chunks: ['index']  // 只引用 index.js
    }),
    // 多入口 - 生成 other.html
    new HtmlWebpackPlugin({
        template: path.join(srcPath, 'other.html'),
        filename: 'other.html',
        chunks: ['other']  // 只引用 other.js
    })
]

```



## 10-5 抽离css 文件

如何把 js 和 css 文件，分别打包？

如果不分开，css 会打包到 js 文件中

打包会生成， index.html, index.[contentHash:8].js   img/ （ 文件夹，图片



开发环境下，可以不做改变。
在线上环境下，抽离。  
webpack.production.js 


>核心：
>在 css loader 中，使用 MiniCssExtractPlugin.loader 取代style-loader

	理解style-loader， 是把css 代码放到 style中，这么一个作用
	然后使用 MiniCssExtractPlugin，重新生成一个css 文件

```
// 分离
const MiniCssExtractPlugin = require('mini-css-extract-plugin')

// 压缩js, 因为uglifyjs不支持es6语法，所以用terser-webpack-plugin替代uglifyjs-webpack-plugin
const TerserJSPlugin = require('terser-webpack-plugin')

// 压缩css 
const OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin')


rules: [
    // 抽离 css
    {
        test: /\.css$/,
        loader: [
            MiniCssExtractPlugin.loader,  // 注意，这里不再用 style-loader
            'css-loader',
            'postcss-loader'
        ]
    },
    // 抽离 less --> css
    {
        test: /\.less$/,
        loader: [
            MiniCssExtractPlugin.loader,  // 注意，这里不再用 style-loader
            'css-loader',
            'less-loader',
            'postcss-loader'
        ]
    }
]

plugins: [
    new CleanWebpackPlugin(), // 会默认清空 output.path 文件夹
    // 抽离 css 文件
    new MiniCssExtractPlugin({
        filename: 'css/main.[contentHash:8].css'
    })
],
optimization: {
    // 压缩 css
    minimizer: [new TerserJSPlugin({}), new OptimizeCSSAssetsPlugin({})],
}
    
压缩后：
在 dist/css 下生成 main.[hashContent:8].css  
css 变成一行，去掉注释
```

## 10-6  抽离公共代码，和 第三方代码

场景： index.js 中引入 第三方库， math.js

other.js 文件中，也引入了第三方库， math.js

开发环境下可以不分割，生产版本，一定要拆出来



缺点：
	直接打包，会在每个文件中，都有 math.js 的代码，那么如何优化呢？
	
	把 math 整个文件打入 业务代码中，如果业务代码改变，contentHash 改变，整个文件重新打包，影响打包速度。


改变 webpack.prod.js

```
optimization: {
	splitChunks: {
	    chunks: 'all',
	    /**
	     * initial 入口 chunk，对于异步导入的文件不处理
	        async 异步 chunk，只对异步导入的文件处理
	        all 全部 chunk
	     */
	
	    // 缓存分组
	    cacheGroups: {
	        // 第三方模块
	        vendor: {
	            name: 'vendor', // chunk 名称
	            priority: 1, // 权限更高，优先抽离，重要！！！
	            test: /node_modules/,
	            minSize: 0,  // 大小限制, 当第三方模块代码大小 > 这个值时候，单独打包出来
	            minChunks: 1  // 最少复用过几次
	        },
	
	        // 公共的模块
	        common: {
	            name: 'common', // chunk 名称
	            priority: 0, // 优先级
	            minSize: 0,  // 公共模块的大小限制
	            
	            // 比如抽离的组件，如果在整个项目中只使用了一次，不要抽出来。
	            minChunks: 2  // 公共模块最少复用过几次, 假如只用过一次，没必要单独拆分出来
	        }
	    }
	}
}

```

打包结果： 生成 index.js, vendor.[hash:8].js,  common.[hash:8].js

```
plugins: [
    // 多入口 - 生成 index.html
    new HtmlWebpackPlugin({
        template: path.join(srcPath, 'index.html'),
        filename: 'index.html',
        // chunks 表示该页面要引用哪些 chunk （即上面的 index 和 other），默认全部引用
        chunks: ['index', 'vendor', 'common'] 
    }),
    // 多入口 - 生成 other.html
    new HtmlWebpackPlugin({
        template: path.join(srcPath, 'other.html'),
        filename: 'other.html',
        chunks: ['other', 'common']  
    })
]
```



chunks 来源：

+ entry
+ splitChunks
+ 异步文件



## 10-7 异步加载


正常同步加载文件，通过在 文件首部，import 文件

异步：通过import 返回 promise 方式

```
import imgFile from './img/1.png'  //同步

异步，使用 import() 方法

setTimeout(()=> {
	
	import('./dynamic-data.js').then(res=> {
		// todo
	})
}, 1500)
```

webpack 会为 异步代码 单独生成一个文件。

和 common.1234568.js 同一个文件夹下生成一个文件。



## 10-8  module chunk bundle 区别

module： webpack 中一切皆模块， 只有可以被引用的文件，都是模块

chunk： 多模块合并成的， 如 entry import() splitChunk

> chunk三大来源

	entry
	splitChunks
	import()


​	
​	
是一系列模块文件生成的文件
​	
​	比如 entry index.js 文件，可能不止index.js 这一个文件，把这个页面引用的 其他文件，生成 chunk
​	
	比如异步引入文件，import().then 生成一个 chunk
	
	比如，把所有的第三方库，生成一个 chunk
	
	把 公共模块，生成一个chunk

bundle： 最终的输出文件，在 dist 文件下生成的文件。


## 10-9 webpack 性能优化

常见：

	开发环境下，优化打包构建速度，提示开发体验和效率
	生产环境下，优化产出代码，产品性能


构建速度：
​	

	更小
		因为uglifyjs不支持es6语法，所以用terser-webpack-plugin替代uglifyjs-webpack-plugin	
		TerserJSPlugin，js压缩
		OptimizeCSSAssetsPlugin， css压缩
		ignorePlugin，不引入无用模块，比如引入 moment日期内库，默认引入所有语音js代码，只引入中文、英文即可
		noparse，引入不编译, 不进行模块化分析
	
	更快
		happyPack  多进程打包，注意不是多线程。 
		paralleUglify  多进程压缩js
	
	缓存
		优化 babel-loader，开启缓存，cacheDirectory，明确范围，include，exclude，不用每次重新编译
	
	性能
		自动刷新
		热更新  浏览器不刷新，代码已生效


优化babel-loader
	

	 {
	    test: /\.js$/,
	    use: ['babel-loader ? cacheDirectory'],  // 使用缓存，只要es6代码没有改的，不用重新编译，
	    include: srcPath,  // include 和 exclude 只写一个就可以
	    exclude: /node_modules/  排除范围
	},


​	
## 10-10 ignorePlugin、noparse

moment.js 前端使用的处理日期的内库
	
默认会引入所有语音js代码，代码过大

如何只引入中文和英文？



```	
plugins:[	
 // 忽略 moment 下的 /locale 目录
 new webpack.IgnorePlugin(/\.\/locale/, /moment/),
]	
	
项目中：
import moment from 'moment'
import 'moment/locale/zh-cn' // 手动引入中文语音包
moment.local('zh-cn') //设置语音为中文
```


ignorePlugin 直接不引入，代码中没有

noParse 引入，但是不打包, (不编译，不进行模块化分析




#### noparse 避免重复打包

一般 min.js 是模块化处理过的文件，无需在做模块化分析

比如 react.min.js 是已经模块化分析后的文件，不需要重新打包。


```javascript

module: {

noParse: [/react\.min\.js$/]

}


```

## 10-11 happyPack 多进程打包、 paralleUglify 多进程压缩js

js 单线程，开启多进程打包

提高构建速度，特别是多核cpu

```
const HappyPack = require('happypack')

rules: [
    // js
    {
        test: /\.js$/,
        // 把对 .js 文件的处理转交给 id 为 babel 的 HappyPack 实例
        use: ['happypack/loader?id=babel'],
        include: srcPath,
        // exclude: /node_modules/
    },

plugins: [
	// happyPack 开启多进程打包
    new HappyPack({
        // 用唯一的标识符 id 来代表当前的 HappyPack 是用来处理一类特定的文件
        id: 'babel',
        // 如何处理 .js 文件，用法和 Loader 配置中一样
        loaders: ['babel-loader?cacheDirectory']
    }),
]
```

多进程压缩js

```
const ParallelUglifyPlugin = require('webpack-parallel-uglify-plugin')


// 使用 ParallelUglifyPlugin 并行压缩输出的 JS 代码
new ParallelUglifyPlugin({
    // 传递给 UglifyJS 的参数
    // （还是使用 UglifyJS 压缩，只不过帮助开启了多进程）
    uglifyJS: {
        output: {
            beautify: false, // 最紧凑的输出
            comments: false, // 删除所有的注释
        },
        compress: {
            // 删除所有的 `console` 语句，可以兼容ie浏览器
            drop_console: true,
            // 内嵌定义了但是只用到一次的变量
            collapse_vars: true,
            // 提取出出现多次但是没有定义成变量去引用的静态值
            reduce_vars: true,
        }
    }
})
```

关于开启多进程

	项目比较大，打包比较慢，开启进程能提高速度
	项目比较小，打包很快，开启多进程会降低打包速度
	按需使用

## 10-12 自动刷新和热更新

自动刷新

	开发环境 devServer 会有
	生成环境，是不允许的
	
	watch: true, // 开启监听，默认为 false
	watchOptions: {
	    ignored: /node_modules/, // 忽略哪些
	    // 监听到变化发生后会等300ms再去执行动作，防止文件更新太快导致重新编译频率太高
	    // 默认为 300ms
	    aggregateTimeout: 300,
	    // 判断文件是否发生变化是通过不停的去询问系统指定文件有没有变化实现的
	    // 默认每隔1000毫秒询问一次
	    poll: 1000
	}

热更新：

	自动刷新：整个网页全部刷新，速度较慢
	
	自动刷新：整个网页刷新，状态会丢失，比如表单里面的输入会丢失
	
	热更新：新代码生效，网页不刷新，状态不丢失

webpack.dev.js

几个步骤：
	
1. 引入HotModuleReplacementPlugin 插件
2. 改变 entry 中 入口文件网址
3. hot: true
4. module.hot.accept

```
const HotModuleReplacementPlugin = require('webpack/lib/HotModuleReplacementPlugin');

// entry 改变
entry: {
// index: path.join(srcPath, 'index.js'),
index: [
    'webpack-dev-server/client?http://localhost:8080/',
    'webpack/hot/dev-server',
    path.join(srcPath, 'index.js')
],

// plugins 改变
plugins: [
    new HotModuleReplacementPlugin()
],

// devServer hot 设置为true
devServer: {
    hot: true,
}
```

开启热更新，当页面的css 代码改变，显然符合，上述的配置已经能够满足需求

如果页面的js代码改变了，还是想保持热更新呢？
	
	if(module.hot) {
		module.hot.accept(['./math'], ()=> {
		
		})
	}


表示什么意思?
	
	注册一个监听范围，监听范围内，修改，触发热更新
	允许哪些模块进行热更新监听， 


## 10-13 DllPlugin 动态链接库

+ 前端框架如 vue 、 react ，体积大，构建慢   
+ 比较稳定，不常升级版本  
+ 同一个版本只构建一次即可，不用每次都重新构建  



+ webpack 已经内置 DllPlugin 支持
+ 通过DllPlugin : 打包出dll 文件
+ 通过DllReferencePlugin: 使用 dll 文件



dist 目录下输出了两个文件

```
react.dll.js  //react chunk 文件
react.mainfest.json  // react内容的索引
```

目的：
为了打包出 库和索引
需要配置 dll文件的引入地址


首先需要专门写一个  webpack.dll.js 文件

```
module.exports = {
  mode: 'development',
  // JS 执行入口文件
  entry: {
    // 把 React 相关模块的放到一个单独的动态链接库
    react: ['react', 'react-dom']
  },
  output: {
    // 输出的动态链接库的文件名称，[name] 代表当前动态链接库的名称，
    // 也就是 entry 中配置的 react 和 polyfill
    
    filename: '[name].dll.js',
    // 输出的文件都放到 dist 目录下
    path: distPath,
    // 存放动态链接库的全局变量名称，例如对应 react 来说就是 _dll_react
    // 之所以在前面加上 _dll_ 是为了防止全局变量冲突
    library: '_dll_[name]',
  },
  plugins: [
    // 接入 DllPlugin
    new DllPlugin({
      // 动态链接库的全局变量名称，需要和 output.library 中保持一致
      // 该字段的值也就是输出的 manifest.json 文件 中 name 字段的值
      // 例如 react.manifest.json 中就有 "name": "_dll_react"
      name: '_dll_[name]',
      // 描述动态链接库的 manifest.json 文件输出时的文件名称
      path: path.join(distPath, '[name].manifest.json'),
    }),
  ],
}
```

在 package.json 文件中，我们配置，生成 dll 文件的命令

```
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "dev": "webpack-dev-server --config build/webpack.dev.js",
    "dll": "webpack --config build/webpack.dll.js"
  },
```

然后我们在dist目录中，生成 react.dll.js 和 react.manifest.json
文件



使用：
webpack.dev.js  文件：

```
// 第一，引入 DllReferencePlugin
const DllReferencePlugin = require('webpack/lib/DllReferencePlugin');

...


plugins: [
    new webpack.DefinePlugin({
        // window.ENV = 'production'
        ENV: JSON.stringify('development')
    }),
    // 第三，告诉 Webpack 使用了哪些动态链接库
    new DllReferencePlugin({
        // 描述 react 动态链接库的文件内容
        manifest: require(path.join(distPath, 'react.manifest.json')),
    }),
],

```

两个文件分别在哪里引用？

dll.js 页面引用

index.html

```
<script src="./react.dll.js"></script>

```
manifest.json 在  plugins中配置，如上所示


## 10-14 webpack优化构建速度

总结：可用于 生产环境  

```
更小
	IgnorePlugin   
	noParse  
更快
	happyPack    
	parallelUglifyPlugin 必须生产缓存  
缓存
	优化babel-loader, 缓存 + 排除文件夹 , include, exclude  
```
不可用于生产环境  

	自动刷新  
	热更新  
	DllPlugin

## 10-15 性能优化——产出代码

+ 体积更小
+ 合理分包，不重复加载
+ 速度更快，内存使用更快


```

代码分割
	第三方代码，公共代码抽出， splitchunks, cacheGroups

tree shaking
	使用production
	
	自动代码压缩

异步加载
	懒加载，组件的异步加载
	
优化
	bundle 加 hash， 内容hash编码 [name].[contentHash:8].js
	小图base64 编码  		
	Scope Hosting
更小
	ignorePlugin, 打包代码更少
	
更快
	使用 CDN 加速, 把打包文件，放到 cdn 服务器上去，让 publicPath 可访问
	作用：让所有静态文件 url 的前缀
	
	output: {
	    filename: '[name].[contentHash:8].js', // name 即多入口时 entry 的 key
	    path: distPath,
	    // publicPath: 'http://cdn.abc.com'  // 修改所有静态文件 url 的前缀（如 cdn 域名），这里暂时用不到
	},
```


## 10-16  tree shaking


在开发环境下，不需要进行 tree shaking

生产环境下，我们希望，库的代码中，没有使用过的方法，不要打包进去

如何开启： 
	
	mode: 'production' // 自动开启

只有 es6 module 才能让 tree shaking 生效  
common.js 不行


## 10-17 es6 module 和 common js


ES6 module 静态引入，编译时引用
	
	无条件引入
	打包的时候，能直接识别到
	不能在条件中引用，不能通过代码判断，是否能用

commonjs 动态引入，执行时引入
	
	打包的时候不知道是啥，只有执行的时候才能引用

只有 es6 module 才能静态分析， 实现 tree shaking

webpack打包的时候，代码还没有执行，只是静态分析，代码还没有正式被使用

```
let apiList = require('../config/api.js')
if(isDev) { 
  //执行的时候才知道，里面的代码是否会执行
  // 执行的时候才知道 isDev 是否执行
  apiList = require('../config/api_dev.js')
  
  //编译时候回报错，只能静态引入
  import apiList from '../config/api.js'
}


import apiList from '../config/api.js'

import不能在逻辑判断里面。在编译时就知道，是否能引入。

```

## 10-18 Scope Hosting

```
// hello.js
export default 'hello'

//index.js
import str from './hello.js'
console.log(str);
```
打包后，是两个函数

```
(function(){})	
(function(){})	
```

每个文件都会产生一个函数，每个函数产生一个作用域，  
对内存消耗不友好

希望能够合并成一个函数。

```
(function(){

})	
```

+ 代码体积更小
+ 创建函数作用域更少
+ 代码可读性更好

如何配置：

#### 没看懂，todo



## 10-19 babel 串讲

+ 环境搭建 & 基本配置
	
		.babelrc 配置
		presets 和 plugins
		
		preset-env： 常用的plugin语法集合。
		
		//.babelrc
		{
		    "presets": ["@babel/preset-env", "@babel/preset-react"],
		    "plugins": []
		}


​	
+ babel-polyfill
	
	
		core.js 集成了 es6、es7 新语法，补丁
		regenerator 库， 有一些语法 core.js 中没有
		
		babel-polyfill 是core.js 和 regenerator 的合集，满足所有 es6、es7的语法
		
		对不支持某些语法的浏览器，自己实现，polyfill 帮你转成 es5方法
		
		对浏览器当前情况，做补充和兼容
	
	

​	
​	
+ babel-runtime

babel 和 polyfill区别：	

	Babel:Babel 是一个广泛使用的 ES6 转码器，可以将 ES6 代码转为 ES5 代码。
	注意：Babel 默认只转换新的 JavaScript 句法（syntax），如，箭头函数语法，而不转换新的 API。 如 Array、Map。
	
	Polyfill:Polyfill的准确意思为，用于实现浏览器并不支持的原生API的代码。


## 10-21  babel-polyfill 如何按需使用？




npx babel index.js // npx 中，使用 babel 转为 es5 语法


+ 文件比较大，
+ 只有一部分功能，无需全部引入
+ 配置按需引入

```
{
    "presets": ["@babel/preset-env", {
    	"useBuiltIns": "usage",
    	"corejs": 3  // corejs 版本
    }],
    "plugins": []
}
```



## 10-22 babel-runtime

babel-polyfill 缺点：

想要在全局环境下使用 promise， 得在全局环境下，添加API。


```
// 污染全局环境
window.Promise = function() {}
Array.prototype.includes = function () {}
```

问题出在：如果想做成一个库，给别人使用，就不能使用 ES6 官方定义的 名字，防止冲突。


```
// 使用方，由于某些请求，重新定义全局的 Promise 呀

window.Promise = 'abc'
Array.prototype.includes = 100
```

解决：	
把index.js 中 promise 等，变成内部 _promise

	_promise
	_includes


```
"plugins": [
    [
        "@babel/plugin-transform-runtime",
        {
            "absoluteRuntime": false,
            "corejs": 3,
            "helpers": true,
            "regenerator": true,
            "useESModules": false
        }
    ]
]
```

作用： 把index.js 中 promise 等，变成内部 _promise

重新取个变量名，不会污染全局环境


## 10-23 webpack review 

+ 拆分配置和merge
+ 启动本地服务
+ 处理es6, babel
+ 处理样式
+ 处理图片

+ 多入口
+ 抽离css文件
+ 抽离公共代码
+ 懒加载语法

+ 优化。生产环境
+ 优化。开发环境



## 10-24 webpack 面试题

1. 前端为何要打包和构建

	代码层面：
	
	体积更小（Tree-Shaking， 压缩，合并），加载更快  
	编译高级语言或者语法，（ts, ES6, 模块化， Scss  
	兼容性和错误检查（polyfill, postcss ， eslint  
	
	
	工程化层面：
	
	统一、高效的开发环境  
	统一的构建流程和产出标准  
	集成公司构建规范（提测、上线
	
	
	
2. loader plugin 有什么区别

	loader 模块转换器，如 less -> css  
	plugin 转换后再做扩展，扩展插件，如 htmlWebpackPlugin 
	
3. 哪些常见的 loader plugin

	图片，file-loader,  url-loader  
	css，style-loader, css-loader,  postcss-loader, sass-loader, less-loader  
	
	js, babel-loader
	

	plugin:
	
	 DllPlugin	
	 HotModulePlugin	
	 MiniCssExtractPlugin	
	
	  
## 10-25

1. babel 和 webpack 区别

		babel 语法编译工具，不关心模块化
		
	
		webpack 打包构建共建，多个 loader plugin 集合
	
2. 如何产出一个lib
	
		output: {
			filename:
			path:
			libaray:    // 这个参数。
		}
	
2. babel-polyfill 和 babel-runtime 区别
	
		前者会污染全局，后者不会   
		如果是产出第三方 lib 要使用 babel-runtime   

3. 如何使用懒加载

		import('a.js').then
		
		vue 异步组件： components: {
		 border: ()=> import
		
		}
		
		vue-router， 异步路由懒加载
		
		component: ()=> import 
	
4. 为何 proxy 不能被 polyfill

		class 可以通过 function模拟  
		promise可以使用callback 模拟  
		proxy 无法使用 Object.defineProperty 无法模拟
	
## 10-26  webpack 优化， 之前提过，略







