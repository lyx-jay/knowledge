## sticky 布局失效的原因
1. 没有设置 top、right、bottom、left的值。其中，bottom 和 top 同时设置时，top的优先级较高。left 和 right 同时设置时，left 的优先级较高。　
2. 设定为 `position: sticky` 的元素的祖先节点的 `overflow` 属性的值必须为 `visible`。否则，`sticky` 布局就会失效。
3. 在以上基础上，设定了`position: sticky`元素的父容器的高度必须大于当前元素的高度（等于也不行），否则，sticky 就会失效。