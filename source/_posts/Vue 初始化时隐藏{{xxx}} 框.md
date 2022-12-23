---
title: Vue 初始化时隐藏{{xxx}} 框
date: 2019-07-23 13:49:20
tags:
 - Vue
 - 经验
categories:
 - Vue
---

添加样式:
```css
[v-cloak] {
    display: none!important;
}
```

在标签上加上 `v-cloak` 
```html
<div class="topic-page" id="paper_list" v-cloak="">
```