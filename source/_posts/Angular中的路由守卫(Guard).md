---
title: Angular中的路由守卫(Guard)
date: 2020-11-23 13:55:16
tags:
 - Angular
 - Guard
 - 路由守卫
categories:
 - Angular
---

## <center>11.路由守卫-Guard<center>
#### 1.Guard
```
    有四种类型:
        1.canActivate: 控制是否允许进入路由
        2.canActivateChild: 等同于canActivate，只不过针对是所有子路由
        3.canDeactivate: 控制是否允许离开路由
        4.canLoad: 控制是否允许延迟加载整个模块
    
```


| 属性名           | 接口名                    |
| ---------------- | ------------------------- |
| canActivate      | CanActivate               |
| canActivateChild | CanActivateChild          |
| canDeactivate    | CanDeactivate<TComponent> |
| canLoad          | CanLoad                   |

> `canDeactivate` 需要指明具体的组件类名以外，其他接口只是将首字母大写而已。假定需要一个某个角色才能访问某些路由，就需要一个 `CanActivate` 守卫类。

#### 2. 如何使用
```
    { path: 'auth', component: GuardAuthComponent, canActivate: [ CanAuthProvide ] },
    { path: 'admin', loadChildren: './admin/admin.module#AdminModule', canLoad: [ CanAuthProvide ] }
```