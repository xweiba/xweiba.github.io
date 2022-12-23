---
title: Angular中的服务Service
date: 2020-11-23 13:53:11
tags:
 - Angular
 - Service
categories:
 - Angular
---

## <center>3.Angular中的服务</center>
#### 1. 服务Service
> Angular中组件和组件之间是不能相互调用以及通讯的,因此,一般的公共方法都写到服务(Service)中.  
> 服务之间是可以相互调用的
```
    1.创建服务(Service)
    ng g service 目录/服务名称
    通过此命令创建的组件,会在app.module.ts中import引入此服务,并且在@NgModule的providers中添加
    
    2.使用服务
    需要在组件中先import引入此服务,在通过构造方法,将服务赋给组件,e.g: 
    export class MyComponent Implements OnInit {
        constructor(public storageService:StorageService){
            ...
        }
    }
```