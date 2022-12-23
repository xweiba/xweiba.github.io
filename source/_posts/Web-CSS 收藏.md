---
title: Web-CSS收藏
date: 2022-12-23 13:46:48
tags:
 - Web
 - CSS
categories:
 - CSS
---

# 水平居中
参考自: [css的div垂直居中的方法，百分比div垂直居中](https://www.haorooms.com/post/css_div_juzhong)

HTML:
```html
<div class="father">
	<div class="son">未查询到模板信息, 请先添加模板!</div>
</div>
```

CSS:
```css
.father {
	position: fixed;
	width: 100%;
	height: 100%;
	top: 0;
	left: 0;
}

.son {
	position: absolute;
	left: 0;
	bottom: 0;
	right: 0;
	height: 50%;
	text-align: center;
	color: red;
}
```

# 隐藏滚动条
`Chrome` 和 `Safari` :
```css
.element::-webkit-scrollbar { width: 0 !important }
```
`IE 10+` :
```css
.element { -ms-overflow-style: none; }
```
`Firefox` :
```css
.element { overflow: -moz-scrollbars-none; }
```