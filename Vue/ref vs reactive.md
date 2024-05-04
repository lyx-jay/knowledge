## 先验知识

在 vue3 版本中，**响应式方数据是基于 proxy 实现的。[Proxy](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy) 可以为其他对象创建一个代理对象。所谓代理，指的是对一个对象基本语意的代理。它允许我们拦截并重新定义对一个对象的基本操作。但是，proxy 无法提供对原始值（Boolean、Number、BigInt、String、Symbol、undefined、null）的代理。在 JavaScript 中，原始值是按照值传递的，而非是按照引用传递，这意味着，如果一个函数接收原始值作为参数，那么行参与实参之间没有引用关系，它们是两个完全独立的值，对行参的修改无法影响到实参。

因此，为了处理原始值类型和非原始值类型的数据，vue 提供了 `reactive` 和 `ref` 两种方法创建响应式对象。

## reactive

本文主要是为了探究 reactive 和 ref 的区别，因此不会对 reactive 的源码实现深入探讨。

```vue
<script setup>
import { reactive, ref } from 'vue'

const count = ref(0)

const obj = reactive({
	list: [1, 2, 3]
	name: 'reactive',
	count
})

console.log(obj.name) // reactive
console.log(obj.list) // [1, 2, 3]

console.log(obj.count) // good 0
console.log(obj.value.count) // bad

</script>
```


1. `reactive` 接收的参数为非原始类型的值
2. 返回的是一个 proxy 实例对象
3. 使用 reacitve 创建的响应式对象在 script 中无需使用 `.value` 的方式访问。这是因为，reactive 具有自动脱 ref 的能力，降低了用户在使用时的心智负担。
4. 在使用 watch 对 obj 进行监听时，会自动进行深度监听，无需显示声明 `{deep: true}`

## ref

为了解决非原始值无法使用 `proxy` 进行代理的问题，`vue` 采用的方案是使用一个对象包裹非原始值，再使用 ` reactive ` 函数创建响应式对象

```js
function ref(val) {
	const wrapper = {
 		value: val
 	}
 	// Object.defineProperty` 定义了不可枚举的属性 `__v_isRef`。该属性在实现自动脱 ref 能力时会用到。如果是 ref，则返回 value 属性，否则返回本身。
 	Object.defineProperty(wrapper, '__v_isRef', {
 		value: true
 	})
 	return reactive(wrapper)
}

```

```vue
<script setup>
import { reactive, ref } from 'vue'

const count = ref(0)

console.log(count.value)

</script>
```

这样，就可以在 `script` 中使用 `count.value` 访问创建的数据了
`
在模版 `template` 中，可以不使用 `.value` 的形式访问数据，这是因为 vue 实现了自动脱 ref 的能力。Vue 组件中的 `setup` 函数所返回的数据会传递给 `proxyRefs` 函数进行处理

```js
function proxyRefs(target) {
	return new Proxy(target, {
		get(target, key, receiver) {
			const value = Reflect.get(target, key, receiver)
			return value.__v_isRef ? value.value : value
		}
	})
}
```

因此，ref 的特点如下：
1. ref 既可以接受原始类型的值，也可以接收非原始类型的值
2. `ref` 返回的是一个 `RefImpl ` 对象
3. 在 `script` 中，需要使用 `.value` 的方式访问数据
4. `watch` 默认情况下不会深度监听 `ref`

表格对比如下：

|             | ref      | reactive |
| ----------- | -------- | -------- |
| 参数          | 原始值、非原始值 | 非原始值     |
| 返回值         | RefImpl  | Proxy 实例 |
| script 使用方式 | .value   | 直接使用     |
| Watch       | 非深度监听    | 深度监听     |

