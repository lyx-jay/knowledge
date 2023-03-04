```html
<label class="my-checkbox">
  <input type="checkbox" />
  <span>a checkbox</span>
</label>

<style>
/* your code here */

.my-checkbox {

	display: flex;
	
	align-items: center;

}

/* 未选中 checkbox 的样式 */
input[type="checkbox"] {
	/* appearance 设置为 none 可以消除原本的盒子样式 */
	appearance: none;
	
	width: 10px;
	
	height: 10px;
	
	border: none;
	
	border-radius: 5px;
	
	background-color: gray;

}

  
/* 选中 checkbox 的样式 */
input[type="checkbox"]:checked {

	background-color: green;

}
</style>
```