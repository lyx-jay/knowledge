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
