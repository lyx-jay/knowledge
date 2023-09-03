#vue #diff #double-diff

双端diff算法可以理解为双指针法，通过设定首尾两个指针来减少循环次数

对于`newNodeList` 和 `oldNodeList` 分别设置两个指针，
`newNodeList: newStartIndex, newEndIndex`
`oldNodeList: oldStartIndex, oldEndIndex`

设置四种基础的校验顺序：
1. 比较 `newNodeList[newStartIndex]` 和 `oldNodeList[oldStartIndex]`
2. 比较 `newNodeList[newEndIndex]` 和 `oldNodeList[oldEndIndex]`
3. 比较 `newNodeList[newStartIndex]` 和 `oldNodeList[oldEndIndex]`
4. 比较 `newNodeList[newEndIndex]` 和 `oldNodeList[oldStartIndex]`

```js
function dublePointerDiff(newNodeList, oldNodeList) {
	let newStartIndex = 0
	let newEndIndex = newNodeList.length - 1
	let oldStartIndex = 0
	let oldEndIndex = oldNodeList.length - 1

	let newStartVNode = newNodeList[newStartIndex]
	let newEndVNode = newNodeList[newEndIndex]
	let oldStartVNode = oldNodeList[oldStartIndex]
	let oldEndVNode = oldNodeList[oldEndIndex]
	while (newStartIndex <= newEndIndex && oldStartIndex <= oldEndIndex) {
		if (!oldStartIndex) {
			oldStartVNode = oldNodeList[++oldStartIndex]
		} else if (!newStartIndex) {
			newStatrVNode = newNodeList[++newStartIndex]
		}
		if (newStartVNode.key === oldStartVNode.key) {
			// 指针移动
			newStartIndex++
			oldStartIndex++
		} else if (newEndVNode.key === oldEndVNode.key) {
			newEndIndex--
			odlEndIndex--
			
		} else if (newStartVNode.key === oldEndVNode.key) {
			newStartIndex++
			oldEndIndex--
		
		} else if (newEndVNode.key === oldStartVNode.key) {
			newEndIndex--
			oldStartIndex++
		
		} else {
			// 不符合这四种情况
			// 寻找新节点数组中的头部节点在旧数组节点中的位置
			let idxInOld = oldNodeList.find(vnode => vnode.key === newStartVNode.key)
			if (idxInold > 0) {
				const vnodeToMove = oldNodeList[idxInOld]
				// 更新节点
				patch(vnodeToMove, newStartVNode, container)
				// 移动节点位置
				insert()
				// 将移动过的节点置为undefined
				oldNodeList[idxInOld] = undefined
			} else {
				// idxInOld表示该节点是新节点
				mount(newStartVnode)
			}
			newStartIndex++
		}
	}
	if (oldEndIndex < oldStartIndex &&  newSatrtIndex <= newEndIndex) {
		// 挂载新节点
		for (let i = newStartIndex; i <= nwEndIndex; i++) {
			patch(null, newNodeList[i], container, oldStartVNode.el)
		}
	} else if (newEndIndex < newStartIndex && oldStartIndex <= oldEndIndex) {
		// 卸载旧节点
		for (let i = oldStartIndex; i <= oldEndIndex; i++) {
			unmount(oldNodeList[i])
		}
	}
}
```