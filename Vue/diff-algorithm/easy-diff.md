#vue #diff #easy-diff

简单 Diff 算法可以理解为使用【双重暴力循环法】找到可复用的节点。

```js
// 伪代码如下
const newNodeList = [
	{
	  type: 'p',
	  key: 1
	},
	{
	  type: 'p',
	  key: 2
	},
	{
	  type: 'p',
	  key: 3
	},
	{
	  type: 'p',
	  key: 4
	},
]
const oldNodeList = [
	{
	  type: 'p',
	  key: 4
	},
	{
	  type: 'p',
	  key: 2
	},
	{
	  type: 'p',
	  key: 3
	},
	{
	  type: 'p',
	  key: 1
	},
]

function easyDiff(newNodeList, oldNodeList) {
	// lastIndex 表示新节点在旧节点中的最大索引
	let lastIndex = 0
	for (let i = 0; i < newNodeList.length; i++) {
		const newNode = newNodeList[i]
		for (let j = 0; j < odlNodeList.length; j++) {
			const oldNode = oldNodeList[j]
			if (newNode.key === oldNode.key) {
				// 比较两个节点，决定是否进行卸载、挂载、更新文本
				patch(oldNode, newNode, container)
				// j 代表新节点在旧节点数组中的index
				if (j < lastIndex) {
					// prevNode 是 newNode 的前一个节点
					const prevNode = newNodeList[i - 1]
					if (preNode) {
						// 锚点元素为prevNode对应真实DOM的下一个兄弟节点
						const anchor = preNode.el.nextSlibing
						// 插入节点函数
						insert(newVNode.el, container, anchor)
					}
				} else {
					lastIndex = j
				}
				break
			}
		}
	}
	// 移除不存在的节点
	for (let i = 0; i < oldNodeList.length; i++) {
		const oldNode = oldNodeList[i]
		const has = newNodeList.find(vnode => vnode.key === oldNode.key)
	}
	if (!has) {
		unmount(oldNode)
	}
}
```
- [ ] 补充图解 