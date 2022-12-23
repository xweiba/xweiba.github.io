---
title: Angular中的指令
date: 2020-11-23 13:56:50
tags:
 - Angular
 - 指令
categories:
 - Angular
---

## <center>14.Angular中的指令</center>

#### 1. 指令类型
> Angular 中有三种类型的指令  
> 1. 组件 — 拥有模板的指令
> 2. 结构型指令 — 通过添加和移除 DOM 元素改变 DOM 布局的指令
> 3. 属性型指令 — 改变元素、组件或其它指令的外观和行为的指令


#### 2. 属性指令
> 创建指令: ng generate directive xxxxx
```
import { Directive } from '@angular/core';

@Directive({
  selector: '[appHighlight]'
})
export class HighlightDirective {

  @Input() highlightColor: string;

  constructor(el: ElementRef) {
     el.nativeElement.style.backgroundColor = this.highlightColor;
  }
  
  @HostListener('mouseenter') onMouseEnter() {
    this.highlight('yellow');
  }
  
  @HostListener('mouseleave') onMouseLeave() {
    this.highlight(null);
  }
  
}

el: ElementRef: ElementRef 通过其 nativeElement 属性给你了直接访问宿主 DOM 元素的能力。

@Input(): 可以接收传递给指令的参数,不命名,默认和变量名称一样,也可以使用别名,即写成其他名字,如指令名,直接通过 [appHighlight]='xxxx',接收参数,并且可以写多个,来接收多个传入的参数

@HostListener(): 让你订阅某个属性型指令所在的宿主 DOM 元素的事件
```



#### 3. 结构指令
> 结构指令的作用是:塑造或重塑 DOM 的结构，比如添加、移除或维护这些元素,
>  NgIf、NgFor和NgSwitch是常见的三个内置的结构型指令  

> 一个宿主元素上只能有一个结构型指令
```
星号（*）前缀: 如*ngIf,星号是一个用来简化更复杂语法的“语法糖”。 从内部实现来说，Angular 把 *ngIf 属性 翻译成一个 <ng-template> 元素 并用它来包裹宿主元素

实际解析代码如下:
<ng-template [ngIf]="hero">
  <div class="name">{{hero.name}}</div>
</ng-template>



import { Directive, Input, TemplateRef, ViewContainerRef } from '@angular/core';

/**
 * Add the template content to the DOM unless the condition is true.
 *
 * If the expression assigned to `appUnless` evaluates to a truthy value
 * then the templated elements are removed removed from the DOM,
 * the templated elements are (re)inserted into the DOM.
 *
 * <div *appUnless="errorCount" class="success">
 *   Congrats! Everything is great!
 * </div>
 *
 * ### Syntax
 *
 * - `<div *appUnless="condition">...</div>`
 * - `<ng-template [appUnless]="condition"><div>...</div></ng-template>`
 *
 */
@Directive({ selector: '[appUnless]'})
export class UnlessDirective {
  private hasView = false;

  constructor(
    private templateRef: TemplateRef<any>,
    private viewContainer: ViewContainerRef) { }

  @Input() set appUnless(condition: boolean) {
    if (!condition && !this.hasView) {
      this.viewContainer.createEmbeddedView(this.templateRef);
      this.hasView = true;
    } else if (condition && this.hasView) {
      this.viewContainer.clear();
      this.hasView = false;
    }
  }
}

Input、TemplateRef 和 ViewContainerRef，你在任何结构型指令中都会需要它们

TemplateRef:取得<ng-template>的内容
ViewContainerRef:访问这个视图容器


该指令的使用者会把一个 true/false 条件绑定到 [appUnless] 属性上。 也就是说，该指令需要一个带有 @Input 的 appUnless 属性。
一旦该值的条件发生了变化，Angular 就会去设置 appUnless 属性。因为不能用 appUnless 属性，所以你要为它定义一个设置器（setter）。

    1.如果条件为假，并且以前尚未创建过该视图，就告诉视图容器（ViewContainer）根据模板创建一个内嵌视图。

    2.如果条件为真，并且视图已经显示出来了，就会清除该容器，并销毁该视图。

没有人会读取 appUnless 属性，因此它不需要定义 getter。

```