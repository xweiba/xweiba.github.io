---
title: Angular中的生命周期函数
date: 2020-11-23 13:58:53
tags:
 - Angular
 - 生命周期
categories:
 - Angular
---

## <center>5.Angular中的生命周期函数</center>
#### 1. Angular中的生命周期函数
```
    生命周期函数执行顺序,从上到下
    
    1. ngOnChanges(): 父子组件互相传值时会触发,首次触发一定在ngOnInit()之前
    
    2. ngOnInit(): 初始化组件以及指令(此时dom并未完全加载),一般将请求数据的操作放到这里
    
    3. ngDoCheck(): 可以做一些自定义操作,来监测数据是否变化
    
    4. ngAfterContentInit(): 组件渲染完成后调用
    
    5. ngAfterContentChecked(): 在每次组件渲染完成后,可以做一些自定义操作
    
    6. ngAfterViewInit(): 视图加载完成,一般在这里进行dom操作
    
    7. ngAfterViewChecked(): 在每次视图加载完成后,可以做一些自定义操作
    
    8. ngOnDestroy(): 组件销毁时触发
```
> 构造方法不属于生命周期函数,在构造方法中,除了对成员变量初始化之外,其他什么都不应该做