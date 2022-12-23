---
title: Angular中的模块
date: 2020-11-23 14:00:40
tags:
 - Angular
 - 模块
categories:
 - Angular
---

## <center>8.Angular中的模块</center>

#### 1. 创建自定义模块
```
    ng g module 路径
    
    创建带路由的模块
    ng g module 路径 --routing
```

#### 2. 使用自定义模块
```
e.g:
    1. 创建自定义模块
        ng g module modules/user --routing
        
    2. 创建模块下子组件
        ng g component modules/user/components/address
        
    3. 创建模块根组件
        ng g component modules/user
        
    4. 在自定义模块中暴露出想在根组件中使用的子组件
    @NgModule({
      declarations: [模块下的子组件],
      imports: [
        CommonModule,
        LiveChannelRoutingModule
      ],
      exports:[想暴露出去的子组件]
    })
    
    5. 在根模块中导入自定义模块,即在app.module.ts文件中修改
    import { 自定义模块 } from './live-channel/live-channel.module';
    
    @NgModule({
      declarations: [
        AppComponent,
        HeaderComponent,
        FooterComponent,
        MenuComponent
      ],
      imports: [
        BrowserModule,
        AppRoutingModule,
        自定义模块,
      ],
      providers: [],
      bootstrap: [AppComponent]
    })
```