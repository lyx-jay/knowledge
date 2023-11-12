```js
export function hasChange(x, y) {
	if (x === y) {
		// 排除掉+0和-0的情况
		return x === 0 && 1 / x !== 1 / y
	} else {
		// 排除掉NaN的情况
		return x === x || y === y
	}
}
```