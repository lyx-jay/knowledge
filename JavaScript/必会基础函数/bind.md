`bind` 函数可以改变 `this` 指向，接受两个参数：
1. 运行函数的作用域
2. 数组或类数组对象

```js
Function.protoytpe.bind = function(context, array) {
	let cxt
	let res
	
  if (context === null || typeof context === 'undefined') {
    cxt = window
  } else {
    cxt = Object(context)
  }

  if (typeof this === 'function') {
    cxt.fn = this
  } else {
    throw new TypeError("this should be function")
  }
  
	if (!array) {
		res = cxt.fn()
	} else {
		const args = new Array(array.length)
		for (let i = 0; i < array.length;i++) {
			args.push(array[i])
		}
		res = cxt.fn(...args)
	}
	
	delete cxt.fn
	return res
}
```