---
title: Angular中表单校验
date: 2020-11-23 14:01:10
tags:
 - Angular
 - 表单效验
categories:
 - Angular
---

## <center>9.Angular中的表单校验</center>
#### 1. 模板驱动表单
```
    1. 需要引入FormsModule
    import { FormsModule } from '@angular/forms';
    
    2. 通过ngModel双向绑定属性,并设置校验规则
    <input id="name" name="name" required minlength="4" appForbiddenName="bob" [(ngModel)]="name" />
    
    3. 在html中设置错误提示
    <div *ngIf="name.invalid && (name.dirty || name.touched)"class="alert alert-danger">
      <div *ngIf="name.errors.required">
        Name is required.
      </div>
      <div *ngIf="name.errors.minlength">
        Name must be at least 4 characters long.
      </div>
      <div *ngIf="name.errors.forbiddenName">
        Name cannot be Bob.
      </div>
    </div>
```

> 优缺点:   
> 只有简单修改html就能完成表单数据绑定和验证  
> 但当表单字段过多,会使HTML过于复杂



#### 2. 响应式表单
> 优缺点:  
> 解耦HTML,针对大表单数据,易于维护  
> 代码编写比模板驱动表单复杂

```
    1. 需要引入ReactiveFormsModule
    import { ReactiveFormsModule } from '@angular/forms';
    
    2. 需要引入formGroup,formControl,FormBuilder,Validators
    import {FormBuilder,FormControl ,FormGroup, Validators} from '@angular/forms';
    
    3. e.g
        HTML:
        - 1. 首先得在html中定义FormGroup对象
        <form [formGroup]="liveChannel" (ngSubmit)="onSubmit()"></form>
        
        - 2. 定义FormController属性变量
        <input id="name" name="name" formControlName="name" />
          
        - 3.设置错误提示HTML
        <span class="error" *ngIf="liveChannel.get('name').hasError('required') && (liveChannel.get('name').touched || liveChannel.get('name').dirty)">频道名称不能为空</span>
        
        TS:
        - 1. 定义FormGroup对象
        liveChannel: FormGroup;
        
        - 2. 绑定表单,通过FormBuilder可简化
        initForm() {
            this.liveChannel = this.fb.group({
              name: ['', [Validators.required, Validators.minLength(4), Validators.maxLength(32)]],
              ...
            });
         }
         
        - 3. 编写提交方法
        onSubmit() {
            for (const i in this.liveChannel.controls) {
              //设置控件状态为已修改值
              this.liveChannel.controls[i].markAsDirty();
              //设置控件状态为已失焦
              this.liveChannel.controls[i].markAsTouched();
              //触发每个formControl的验证事件
              this.liveChannel.controls[i].updateValueAndValidity();
            }
            //判断是否校验通过
            if (this.liveChannel.valid) {
              //获取表单数据...进行后续操作
              console.log(this.liveChannel.value);
            } else {
              console.log('error');
            }
        }
```
> 解释:  
> liveChannel.get('name').hasError('required'):判断该控件是否有包含  required的错误
> liveChannel.get('name').touched: 判断该控件是否有失焦
> liveChannel.get('name').dirty: 判断该控件值是否有修改  
> name: ['', [Validators.required, Validators.minLength(4), Validators.maxLength(32)]]: 第一个参数''是用来绑定控件的初始值,第二个参数[]用于设置该控件的校验规则