#vue #diff #quick

快速 `diff ` 算法在双端 diff 算法的基础上，再次进行了优化。主要增加了两个步骤：
1. 预处理。找到新旧两个 Node 数组中相同的部分
2. 在新节点数组中找到最长连续子数组，作为移动元素的依据

```js
function quickDiff(newNodeList, oldNodeList) {
	// 处理前置节点
	let j = 0
	let newNode = newNodeList[j]
	let oldNode = oldNodeList[j]
	while(newNode.key === oldNode.key) {
		// 更新节点内容
		patch(oldNode, newNode, container)
		j++
		newStart = newNodeList[j]
		oldStart = oldNodeList[j]
	}
	// 处理后置节点
	let newEnd = newNodeList.length - 1
	let oldEnd = oldNodeList.length - 1
	newNode = newNodeList[newEnd]
	oldNode = oldNodeList[oldEnd]
	while (newNode.key === oldNode.key) {
		patch(oldNode, newNode, container)
		newNode = newNodeList[--newEnd]
		oldNode = odlNodeList[--oldEnd]
	}
	// 挂载节点或删除节点
	if (j > oldEnd && j <= newEnd) {
		// 挂载新节点
		const anchorIndex = newEnd + 1
		const anchor = anchorIndex < newNodeList.length ? newNodeList[anchorIndex].el : null
		while (j <= newEnd) {
			patch(null, newNodeList[j++], container, anchor)
		}
	} else if (j > newEnd && j <= oldEnd) {
		// 卸载节点
		while (j <= oldEnd) {
			unmount(oldNodeList[j++])
		}
	} else {
		// 移动节点
		// source 数组用来存储新节点在旧节点数组中的下标，-1 表示该元素需要被挂载
		const count = newEnd - j + 1
		const source = new Array(count).fill(-1)
		const newStart = j
		const oldStart = j
		let pos = 0
		let moved = false
		let patched = 0
		
		const keyIndex = {}
		// 遍历未处理的新节点，构建索引表
		for (let i = newStart; i <= newEnd; i++) {
			keyIndex[newNodeList[i].key] = i
		}
		// 遍历未处理的旧节点
		for (let i = oldStart; i <= oldEnd; i++) {
			const oldNode = oldNodeList[i]
			// 已经更新过的节点数量小于需要更新的节点数量
			if (patched <= count) {
				// 旧节点在新节点数组中的下标位置
				const k = keyIndex[oldNode.key]
				if (typeof k !== 'undefined') {
					newNode = newNodeList[k]
					patch(oldNode, newNode, container)
					patched++
					// i 代表该新节点在旧节点数组中的下标
					source[k - newStart] = i
					if (k < pos) {
						moved = true
					} else {
						pos = k
					}
				} else {
					// k 不存在，表示该 node 在新节点数组中不存在，需要卸载该节点
					unmount(oldNode)
				}
			} else {
				// 表示剩余的节点都是多余的，需要卸载掉
				unmount(oldNode)
			}
		}

		if (moved) {
			// lis 为计算最长递增子序列算法
			const seq = lis(source)
			// i 表示新的一组节点的最后一个元素
			const i = count - 1
			// s 指向递增序列的最后一个元素
			const s = seq.length - 1
			for (let i; i >= 0; i--) {
				if (source[i] === -1) {
					// 需要挂载该节点
					const newNode = newNodeList[i + newStart]
					const anchorIndex = i + newStart + 1
					const anchor = anchorIndex < newNodeList.length ? newNodeList[anchorIndex].el : null
					// 全新的节点需要挂载
					patch(newNode.el, container, anchor)
				} else if (i !== seq[s]) {
					const newNode = newNodeList[i + newStart]
					const anchorIndex = i + newStart + 1
					const anchor = anchorIndex < newNodeList.length ? newNodeList[anchorIndex].el : null
					// 移动的节点需要插入
					insert(newNode.el, container, anchor)
				} else {
					s--
				}
			}
		}
	}
}
```