
`webpack` 作为模块化静态打包工具，实际上并不具备启动本地服务的能力。那我们日常开发时，通过 `webpack` 启动本地服务是如何实现的呢？

实际上，这是 `webpack dev server`提供的能力。`webpack dev server` 主要做了两件事：

1.  创建了一个 `express` 服务，用来管理项目中的静态资源
    
2.  和客户端建立 websocket 长链接，实现消息传递
    

## 创建 express 静态资源服务

首先，我们创建一个 Server 类
```js
class Server {  
​  
  constructor(compiler) {  
		this.app = null         // 存储express实例  
    this.server = null      // 存储静态服务实例  
    this.initialize()       // 初始化函数  
  }  
​  
  initialize() {  
    this.setupApp()  
    this.createServer()  
  }  
}
```


`setupApp` 的功能非常单一，就是负责创建 express 实例
```js
function setupApp() {  
  this.app = new express()  
}
```

`createServer` 函数，则是用来创建 http 服务

```js
function createServer() {
	this.server = http.creaateServer(this.app)
}
```

这时候，我们已经创建了一个 express 服务了。但是，我们还需要对不同的路有进行处理
​

## 建立 websocket 链接

两件事：

-   启动express服务器托管静态资源
    
-   和客户端建立socket链接，实现热更新
    

结构

-   从功能角度写
    
    -   静态服务器相关
        
    -   热更新相关
        
-   整体流程梳理


module.hot.accept 理解