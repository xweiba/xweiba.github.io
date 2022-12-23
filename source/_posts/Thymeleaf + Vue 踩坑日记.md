---
title: Thymeleaf + Vue 踩坑日记
date: 2019-07-25 13:48:43
tags:
 - Thymeleaf
 - Vue
categories:
 - Thymeleaf
---

接收后端传过来的值: 
```js
// 类型切换
var opTypes = new Vue({
    el: '#select_type_area',
    data: {
        // JSON.parse() 转换一下
        types: JSON.parse([[${contentTypes}]]),
        activeType: ''
    },
    methods: {
        selected: function(type) {
            this.activeType = type
        }
    }
});
```

Thymeleaf 不支持 Vue 简写语法, 因为他默认使用的HTML严格检查, 下面的缩写都会报错: 
```html
<!-- 缩写 -->
  <div id="app-11">
    <!-- 完整语法 -->
    <a v-bind:href="url">...</a>

    <!-- 缩写 -->
    <a :href="url">...</a>

    <!-- 完整语法 -->
    <a v-on:click="doSomething">...</a>

    <!-- 缩写 -->
    <a @click="doSomething">...</a>
  </div>
```

取消严格检查:
```pom
<!-- thymeleaf模板对html5标签不使严格检查 -->
<dependency>
	<groupId>net.sourceforge.nekohtml</groupId>
	<artifactId>nekohtml</artifactId>
	<version>1.9.22</version>
</dependency>
```
添加配置: 
```
#默认严格检查
#spring.thymeleaf.mode=HTML5
#非严格检查
spring.thymeleaf.mode=LEGACYHTML5
```

Vue v-for 使用时必须符合语义,
```html
<!-- 此写法, todo.contentName 不会被解析-->
<table class="table01" id="tableList">
	<tr v-for="todo in todos">{{ todo.contentName }}</tr>
</table>

<!-- 正确写法, 加上td标签-->
<table class="table01" id="tableList">
	<tr v-for="todo in todos"><td>{{ todo.contentName }}</td></tr>
</table>

<!-- 兼容合法 -->
<table class="table01" id="tableList">
	<tbody is="button-counter" v-for="item in todos" v-bind:todo="item" v-on:increment="incrementTotal"></tbody>
</table>
```