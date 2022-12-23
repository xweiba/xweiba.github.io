---
title: Angular基础指令
date: 2020-11-23 13:52:09
tags:
 - Angular
categories:
 - Angular
---

## <center>2.Angular基础指令</center>
#### 1. 基础指令
```
    1. 数据绑定: 使用双大括号 {{xxx}},例 {{name}}
    2. 属性绑定: 使用[属性名]="xxx",例 <span [title]="xxx"></span>
    3. html绑定: [innerHTML]="xxx" 
    4. 数据循环
        <li *ngFor="let item of xxx; let key=index">
            {{key}} ---  {{item}}
        </li>
    5. 条件判断: 
        *ngIf="条件表达式"
        
        [ngSwitch]="xxx",e.g:
            <span [ngSwitch]="xxx">
                <p *ngSwitchCase="1">结果1</p>
                <p *ngSwitchCase="2">结果2</p>
                <p *ngSwitchDefault>默认值</p>
            </span>
    6. 动态改变class:
        //flag为后台动态值
        <div [ngClass]="{'red':flag,'blue':!flag}"></div>
    7. 动态改变style: 
        //xxx为后台动态值
        <div [ngStyle]="{'color':xxx}"></div>
    8. 管道
        //例时间格式化管道
        {{date | date:'yyyy-MM-dd HH:mm:ss'}}
        
    9. 事件
        //$event为事件对象,$event.target为当前dom对象
        <button (click)="xxx($event)">点击事件<button>
```
> angular模板中允许简单的运算.e.g: {{1+2}}  
> 其他管道请参考[链接](http://bbs.itying.com/topic/5bf519657e9f5911d41f2a34)


#### 2. 双向数据绑定
```
    1. 双向数据绑定,一般只用于表单,在Angular中使用,需要先引入表单模块
        import { FormsModule } from '@angular/forms';
        @NgModule({
            ...
            imports: [
                BrowserModule,
                FormsModule
            ],
           ...
        }]
        
    2. 双向数据绑定,使用[(ngModel)]='xxx'
        <input type="text" [(ngModel)]="keywords"/>
        <p>{{keywords}}</p>
        
```