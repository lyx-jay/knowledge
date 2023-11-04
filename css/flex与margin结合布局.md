这种布局的核心在与 `margin` 的利用。利用 `margin` 占据 `flex containers` 中的剩余空间
## 一左一右
<div style='display:flex; width: 100%; height: 100px; background: lightgray; align-items: center'>
	<div style='width: 100px; height: 60px; background: lightcoral;'></div>
	<div style='width: 100px; height: 60px; background: lightcoral; margin-left: auto'></div>
</div>

在这个基础上，可以延伸出 *n左m右* 的布局

左-中-右
<div style='display:flex; width: 100%; height: 100px; background: lightgray; align-items: center; gap: 10px'>
	<div style='width: 100px; height: 60px; background: lightcoral;'></div>
	<div style='width: 100px; height: 60px; background: lightcoral; margin: 0 auto'></div>
	<div style='width: 100px; height: 60px; background: lightcoral;'></div>
</div>

两左两右

<div style='display:flex; width: 100%; height: 100px; background: lightgray; align-items: center; gap: 10px'>
	<div style='width: 100px; height: 60px; background: lightcoral;'></div>
	<div style='width: 100px; height: 60px; background: lightcoral;'></div>
	<div style='width: 100px; height: 60px; background: lightcoral; margin-left: auto'></div>
	<div style='width: 100px; height: 60px; background: lightcoral;'></div>
</div>

## 多列布局

<div style='display:flex; width: 100%; height: 100px; background: lightgray; align-items: center;'>
	<div style='width: 50px; height: 60px; background: lightcoral;
	margin: 0 calc((100% - 50px * 5) / 5 / 2)'></div>
	<div style='width: 50px; height: 60px; background: lightcoral;
	margin: 0 calc((100% - 50px * 5) / 5 / 2)'></div>
	<div style='width: 50px; height: 60px; background: lightcoral;
	margin: 0 calc((100% - 50px * 5) / 5 / 2)'></div>
	<div style='width: 50px; height: 60px; background: lightcoral;
	margin: 0 calc((100% - 50px * 5) / 5 / 2)'></div>
	<div style='width: 50px; height: 60px; background: lightcoral;
	margin: 0 calc((100% - 50px * 5) / 5 / 2)'></div>
</div>

这种布局方式可以避免使用 `justify-content: space-between`  时出现的最后一行两个元素一左一右的情况