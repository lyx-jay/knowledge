1. 当宿主文档与 iframe 有相同的二级域名时，通过设置 `document.domain` 属性，可以使浏览器认为 iframe 与宿主文档同源，这样就可以在宿主文档中操作 iframe 内的 DOM 元素，实现跨窗口通信。但是，该属性已经在 web 标准中被废弃掉（虽然仍然有效，但是不建议使用）
2. 目前推荐的跨窗口通信方案是：`postMessage`
 - [ ] 跨窗口的 `cookie`。
	 - [ ] Iframe 中的 cookie 与宿主文档的 cookie 有什么关系？怎样互相影响