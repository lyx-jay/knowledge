## collapse

## collapse-item

## collapse-transition
1. paddingTop 和 paddingBottom 需要做处理，是因为当 height 为 0 时，padding也会占据高度
2. overflow: hidden 是为了让元素形成 #BFC ，让浮动元素也参与计算。浮动元素会让父级元素高度塌陷。

## 涉及到的知识点

#### requestAnimationFrame

