## flex-basic
flex-basic 会覆盖 width的值，如果设置了max-width，其优先级最高
`max-width > flex-basic > width`

默认情况下，`flex-basic` 就是 `width` 的值。

`flex-grow` 与 `flex-shrink` 的计算都与 `flex-basic` 相关

## flex-shrink

在开发中经常遇到的一个情况是：
在一个container容器中，横向放置若干个大小固定的盒子。若所有盒子的宽度之和超出了容器宽度，则每个盒子的宽度就会自动缩小。

<div class='container' style='width:100%; height:100px; border: 1px solid #fff; display: flex; align-items: center'>
	<div style='width: 200px; height: 60px; border: 1px solid red;'></div>
	<div style='width: 200px; height: 60px; border: 1px solid red;'></div>
	<div style='width: 200px; height: 60px; border: 1px solid red;'></div>
	<div style='width: 200px; height: 60px; border: 1px solid red;'></div>
	<div style='width: 200px; height: 60px; border: 1px solid red;'></div>
</div>

这是因为，在 `flex` 布局当中，`flex items` 的 `flex-shrink` 属性的默认值是 1。如果 `flex-items` 的默认宽度之和大于容器宽度（主轴方向），则会按照 `flex-shrink` 进行收缩。

收缩后的每个 `flex-item` 的宽度为：
`width - （(flex-shrink-factor  x flex-basic) / 每个盒子flex-basic与flex-shrink的乘积之和）x 溢出宽度`

如果不想让盒子跟随contianer的宽度变化而收缩，则在每个盒子上设置 `flex-shrink: 0`

<div class='container' style='width:100%; height:100px; border: 1px solid #fff; display: flex; align-items: center'>
	<div style='width: 200px; height: 60px; border: 1px solid red; flex-shrink: 0'></div>
	<div style='width: 200px; height: 60px; border: 1px solid red; flex-shrink: 0'></div>
	<div style='width: 200px; height: 60px; border: 1px solid red; flex-shrink: 0'></div>
	<div style='width: 200px; height: 60px; border: 1px solid red; flex-shrink: 0'></div>
	<div style='width: 200px; height: 60px; border: 1px solid red; flex-shrink: 0'></div>
</div>
