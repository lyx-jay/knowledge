## 基础测试

### props 传递

### data

### 节点数量

测试节点数量时，可以使用 `findAll` API，传入查询条件，可以获得一个 DOM 数组

这里使用到了 `vitest` 中的 `toHaveLength` 方法，具体可以查看[[基础用法]]
```ts
import { mount } from '@vue/test-utils'

test('DOM节点数量', () => {
	const wrapper = mount(YourComponent)
	// 期望有三个 button 节点
	expect(wrapper.findAll('.button')).toHaveLength(3)
})
```