---
title: Java 排序
date: 2022-04-08 16:09:09
tags:
 - 排序

---

## 通过指定 `id` 集排序

在 `select * from table_name where id in ()` 的时候，`MySQL` 会自动按主键自增排序，要是按IN中给定的顺序来取，必须按 `Order by field()` 来实现：

```mysql
SELECT * from `models` where `id` in (111,222,333) order by field(id,111,222,333);
```

但该排序效率较低，可直接在代码中使用 `BeanComparator` 和 `FixedOrderComparator` 来实现对指定 `id` 集排序

```java
public List<XXX> getXxxByIds(List<String> ids) {
    List<XXX> xXXs = xxxInfoDAO.selectByIds(ids);
    xXXs.sort(new BeanComparator("id", new FixedOrderComparator(ids)));
    return XXXs;
}
```
