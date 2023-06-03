`call` 函数可以改变 `this` 指向，并且可以传入若干参数

```js
Function.prototype.call = function (context) {

  let cxt
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

  let args = []
  for (let i = 1; i < arguments.length; i++) {
    args.push(arguments[i])
  }
  const result = cxt.fn(...args)
  delete cxt.fn

  return result
}
```