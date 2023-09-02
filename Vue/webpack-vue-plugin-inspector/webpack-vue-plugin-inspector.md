
作为一名前端开发工程师，在工作中，我们绝大部分时间都在与页面中的DOM元素打交道，那么我们不避免的会经历下面这个过程：

1.  接到需求，修改顶部区域某个按钮的样式
    
2.  打开对应页面的控制台，找到该元素
    
3.  然后再根据元素的 class 名称在 vscode（或其他编辑器）中找到对应的代码位置
    
4.  进行修改
    

如果项目非常复杂或者刚刚接手一个新项目，那么**寻找代码位置**的这个过程就可能会耗费大量的时间。

如何让我们的开发体验变得更好呢？社区的小伙伴已经给出了解决思路，点击页面元素直接跳转到 IDE 中。

下面，让我们来看看这到底是如何实现的呢？

## 原理分析

分析其中的原理，我们可以使用倒推的方法。我们的最终目标是找到该代码在IDE中的位置，那么，必须要具备的一个能力就是**打开IDE**；其次，我们还需要了解目标代码的位置信息，也就是它所在的**文件的路径和行列号信息**;最后，还需要考虑一个问题，**如何获得位置信息**

整个过程其实可以细分为三个阶段：

1.  编译阶段：在DOM中注入元素在编辑器中的位置信息
    
2.  发送请求：点击DOM元素，实际上发送了一个特定的请求，该请求会携带元素的位置信息。
    
3.  打开IDE：在dev server中会拦截相关请求，获取到其中的位置信息。利用 `nodejs` 的能力，打开IDE
    

下面，我们来仔细分析下各个阶段。

### 编译阶段

目前，前端项目，大部分仍然是使用 `webpack` 进行构建。webpack 是一个静态的模块化的打包工具，根据文件依赖关系，会遍历到所有使用的文件。那么，在构建过程中，必然包含有文件的位置信息。

首先，需要将代码转化为 `ast` 树，这一步，可以利用 `@vue/compiler-dom` 这个包实现。

`const ast = compilerDom.parse(code, { comments: true });`

在ast树中就包含我们需要的相关信息，
```js
{  
  children: [  
    {  
      type: 1,  
      ns: 0,  
      tag: 'template',  
      tagType: 0,  
      props: [],  
      isSelfClosing: false,  
      children: [Array],  
      loc: [Object],  
      codegenNode: undefined  
    },  
    {  
      type: 1,  
      ns: 0,  
      tag: 'script',  
      tagType: 0,  
      props: [],  
      isSelfClosing: false,  
      children: [Array],  
      loc: [Object],  
      codegenNode: undefined  
    },  
    {  
      type: 3,  
      content: ' Add "scoped" attribute to limit CSS to this component only ',  
      loc: [Object]  
    },  
    {  
      type: 1,  
      ns: 0,  
      tag: 'style',  
      tagType: 0,  
      props: [Array],  
      isSelfClosing: false,  
      children: [Array],  
      loc: [Object],  
      codegenNode: undefined  
    }  
  loc: {  
    start: { column: 1, line: 1, offset: 0 },  
    end: { column: 1, line: 58, offset: 1882 },  
    source: '<template>\n' +  
      '  <div class="hello">\n' +  
        ...  
      '</template>\n' +  
      '\n' +  
      '<script>\n' +  
        ...  
      '</script>\n' +  
      '\n' +  
      '<!-- Add "scoped" attribute to limit CSS to this component only -->\n' +  
      '<style scoped>\n' +  
        ...  
      '</style>\n'  
  }
```


我们可以在 **loc** 属性中获取到行号和列号信息。同时，在children数组中，可以看到 template 被解析为一个对象，而 template 中又含有 loc 属性，事实上，他们的结构都是相同的。因此，我们可以使用采取递归或其他算法，给每一个DOM添加上位置信息。
```js
export default function compileSFCTemplate(code: string, path: string) {  
  const s = new MagicString(code)  
  const ast = parse(code, { comments: true })  
  transform(ast, {  
    nodeTransforms: [  
      (node) => {  
        if (node.type === 1) {  
          if ((node.tagType === 0) && !EXCLUDE_TAG.includes(node.tag)) {  
            if (node.loc.source.includes(DOM_ATTR)) { return }  
            const insertPosition = node.loc.start.offset + node.tag.length + 1  
            const { line, column } = node.loc.start  
            const content = `${createDomInfo(line, column, path)}`  
​  
            s.prependLeft(insertPosition, content)  
          }  
        }  
      }  
    ]  
  })  
  return s.toString()  
}
```


在完成这一步之后，每一个DOM元素上就会存在其位置信息：

### 发送请求

在编译时，首先会在代码中绑定全局事件监听。这部分代码，也是通过loader的方式添加到客户端当中
```js
  document.addEventListener('keydown', (e) => {  
    key = e.code  
  })  
  document.addEventListener('mousedown', (e) => {  
    if (key === targetKey && (e.button === 1 || e.button === 2)) {  
      e.preventDefault()  
      e.stopImmediatePropagation()  
      e.stopPropagation()  
​  
      const dom = e.target as Element  
      const path = dom!.getAttribute(DOM_ATTR) as string  
​  
      // 执行发送请求函数  
      requestService(path)  
      // reset key  
      key = ''  
    }  
  }, true)
```


当我们使用组合键点击元素时，就会调用 `requestService` 函数发起请求
```js
const requestService = (path: string) => {  
  const { origin } = window.location  
  fetch(`${origin}${OPEN_CODE_IDE}=${path}`).catch((error) => {  
    console.error('dom-to-code: ', error)  
  })  
}
```


`OPEN_CODE_IDE` 是我们自定义的一个字符串，是用来区分其他正常的请求和使用该插件时发送的请求。

### 打开IDE

webpack 使用 `webpack-dev-server` 来启动本地服务。而 `webpack-dev-server` 则是在内部创建了一个 **express 静态资源服务器**，我们可以通过`添加自定义express中间件`的方式来拦截第二步中发出的特定请求。

首先，我们定义一个函数去拦截打开IDE的请求
```js
// 自定义中间件函数  
function yourMiddleware(req, res, next) {  
  // 如果匹配到了特定的标志，就拦下这个请求，打开IDE  
  // 否则，将该请求传递给下一个中间件  
  if (req.url.startWith(OPEN_CODE_API)) {  
    openEditor()  
  } else {  
    next()  
  }  
}
```

在 webpack中，需要在配置文件中的 devServer 里进行设置，`webpack 5` 和 `webpack 4` 设置的选项略有不同:
```js
// webpack v5 - webpack.config.js  
module.exports = {  
  devServer: {  
    setupMiddlewares: (middlewares) => {  
      return [yourMidddleware, ...middlewares]  
    }  
  }  
}  
​  
// webpack v4 - webpack.config.js  
module.exports = {  
  devServer: {  
    // app 就是 express 的实例对象  
    before: (app) => {  
      app.use(yourMiddleware)  
    }  
  }  
}  
```

以 `webpack 5` 为例，我们看看 `webpack-dev-server` 中是如何对 setupMiddlewares 进行处理的：
```js
    if (typeof this.options.setupMiddlewares === "function") {  
      middlewares = this.options.setupMiddlewares(middlewares, this);  
    }  
​  
    middlewares.forEach((middleware) => {  
      if (typeof middleware === "function") {  
        /** @type {import("express").Application} */  
        (this.app).use(middleware);  
      } else if (typeof middleware.path !== "undefined") {  
        /** @type {import("express").Application} */  
        (this.app).use(middleware.path, middleware.middleware);  
      } else {  
        /** @type {import("express").Application} */  
        (this.app).use(middleware.middleware);  
      }  
    });
```


从👆部分源码中可以看到，实际还是调用了 `this.app.use()` 这个方法来使用中间件。

> 需要注意的是：express 中间件只会拦截当前域名发出的请求。例如，你的的程序运行在 `http://localhost:8080/`，那么同域名下的请求都会被拦截。而 `http://localhost:8081/` 下的请求不会被拦截

拦截请求之后，就可以调用 `launch` 函数打开 `IDE`
```js
export const openEditor = (filePath: string, res: Response | ServerResponse<IncomingMessage>) => {  
  if (filePath) {  
    filePath = filePath.replace(OPEN_CODE_IDE + '=', '')  
    launch(filePath, () => {  
      console.log('To specify an editor, specify the EDITOR env variable')  
    })  
    res.end(filePath)  
  } else {  
    res.statusCode = 500  
    res.end('launch-editor-middleware: required query param "filePath" is missing.')  
  }  
}
```

`launch` 函数来源于尤大写的一个包 [launch-editor](https://github.com/yyx990803/launch-editor)，借助这个包的能力，我们可以打开对应的编辑器。至此，整个流程就结束了。
