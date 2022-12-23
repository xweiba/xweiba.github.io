---
title: Angular中的Dom操作以及父子组件通讯
date: 2020-11-23 13:53:55
tags:
 - Angular
 - Dom
 - 父子组件通讯
categories:
 - Angular
---

## <center>4.Angular中的Dom操作以及父子组件通讯</center>
#### 1. 组件中获取Dom对象

```
    组件中的ngOnInit()方法,是组件和指令初始化完成,并不是真正的dom加载完成  
    
    组件中还有一个ngAfterViewInit()方法,这个是视图加载完成,因此建议把dom操作都放在这个方法内
```
#### 2. 通过@ViewChild装饰器获取Dom对象
```
    1.需要在html中给dom起一个名字,e.g:
    <div #myBox></div>
    
    2.在组件中引入viewChild,e.g:
    import {Component,OnInit,ViewChild} from '@angular/core';
    
    3.通过@ViewChild装饰器获取Dom对象,通过类的成员变量,e.g:
        export classs xxx implements OnInit {
            
            @ViewChild("myBox") myBox:any;
            
            constructor(){
                ...
            }
        }
    4.在生命周期函数ngAfterViewInit中操作Dom对象,e.g:
    //nativeElement才是dom对象
    this.myBox.nativeElement
    
```

#### 3. 通过@ViewChild获取子组件
```
    1.在html中引入子组件,并命名,e.g:
    <app-header #header ></app-header> 
    <div #myBox></div>
    
    2.同获取dom对象一样,引入viewChild,创建成员变量接受组件实例,e.g:
     @ViewChild("header") header:any;
    
    4.在生命周期函数ngAfterViewInit中就可以调用子组件的方法:
    this.header.xxx()
```
#### 4. 组件中通讯
```
    1.子组件调用父组件中的属性或方法(父组件 -> 子组件 传值)
        - 1. 父组件调用子组件时,需要传入数据,e.g:
            <app-header [msg]='msg'></app-header>
            
            [msg]:是自定义属性,名称对应子组件中的成员变量
            ='msg':是父组件中所拥有的成员变量
            
        - 2. 子组件中引入Input模块
            import {Component,OnInit,ViewChild,Input} from '@angular/core';
        
        - 3. 子组件中通过@Input接收父组件传过来的数据
            export classs xxx implements OnInit {
            
                @Input() msg:string;
                
                constructor(){
                    ...
                }
            }
        //同理,可传递方法,以及父组件本身(传this),e.g:
            <app-header [msg]='msg' [method]='method' [parent]='this'></app-header>
            
    2.父组件调用子组件中的属性或方法(子组件 -> 父组件 传值)
        可以通过@ViewChild来调用.同上
        
    3.子组件通过@Output广播给父组件传值 (子组件 -> 父组件 传值)
        - 1. 子组件引入Output和EventEmitter,e.g:
            import {Component,OnInit,Output,EventEmitter} from '@angular/core';
        
        - 2. 子组件中实例化EventEmitter
            //相当于注册了一个广播节点
            @Output private outer = new EventEmitter();
            
        - 3. 父组件监听广播,即将广播节点传递给父组件
            <app-footer #footer (outer)='run()'></app-footer>
            
            其中:(outer)对应子组件中广播节点的名称,run()是父组件的方法
            
            
```