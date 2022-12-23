---
title: Thymeleaf 调用java方法
date: 2019-08-23 12:52:34
tags:
 - Thymeleaf
categories:
 - Thymeleaf
---

# Thymeleaf 调用java方法

创建对象调用:

```
<td th:text="${new java.text.DecimalFormat('#00%').format(student.scoringRate)} ">100%</td>
```

静态方法调用:

```
${T(java.lang.Math).abs(student.classRankChangeSum)}
```

调用springbean:

```
${@beanName.getStudents()}
```

