---
title: Thymeleaf VUE 混用奇葩语法
date: 2019-08-08 12:54:19
tags:
 - Thymeleaf 
 - VUE
categories:
 - Thymeleaf 
---

th中使用vue变量
```html
<span th:text="'{{questionResourceType.count}}' + #{UI_38_02_016}">2653道题</span>

<!-- vue th变量混输-->
<span class="lt_1" th:utext="${#messages.msg('UI_38_06_023', '&lt;em&gt;{{questionCount}}&lt;/em&gt;')} + '&lt;em&gt;{{paper.totalPoints}}&lt;/em&gt;' + #{UI_38_01_025}">共20道题 20分</span>
<!-- 渲染后-->
<span class="lt_1">共<em>20</em>道題<em>20</em>分</span>
```



# 常用语法



# 判断条件
```
gt：great than（大于）>
ge：great equal（大于等于）>=
eq：equal（等于）==
lt：less than（小于）<
le：less equal（小于等于）<=
ne：not equal（不等于）!=
```