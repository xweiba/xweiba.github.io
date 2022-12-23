---
title: Angular-踩坑记录
date: 2020-12-01 13:50:28
tags:
 - Angular
 - 错误记录
categories:
 - Angular
---

# 错误记录

### 报错信息:
```js
at Object.emitError (D:\resouces\code\web\content-v5\node_modules\webpack\lib\NormalModule.js:173:6)
at D:\resouces\code\web\content-v5\node_modules\@angular-devkit\build-angular\src\webpack\plugins\postcss-cli-resources.js:125:28
at runMicrotasks (<anonymous>)
at processTicksAndRejections (internal/process/task_queues.js:97:5)
```
错误现象: 页面返回 `Cannot GET /`

解决方法: 是因为插件中的`css`中引用了不存在的资源或路径错误导致的, 由于`css`或`js`做过压缩处理, 所有代码都会格式化成了一行, 所以不会爆出错误的具体位置, 将添加的静态资源格式化后再`bulid`就可以看到错误位置了.

### 报错信息:
```
Error: src/app/content-resource/content-resource.component.html:2:3 - error NG8001: 'app-header' is not a known element:
1. If 'app-header' is an Angular component, then verify that it is part of this module.
2. If 'app-header' is a Web Component then add 'CUSTOM_ELEMENTS_SCHEMA' to the '@NgModule.schemas' of this component to suppress this message.

2   <app-header></app-header>
```

错误现象: 找不到依赖模块的内置组件

解决方法: 在模块的`@NgModule`注解中增加`exports`属性并添加对应组件. 不exports的组件，只能在当前模块中（也就是 declarations 中有此组件的那个模块）中使用.
其他模块是无法使用的.


### 报错信息:
HttpClient 请求报错: 
```
Unexpected token i in JSON at position 12 at JSON.parse (<anonymous>) at XMLHttpRequest.onLoad
```

错误现象: 请求失败

解决方法: 是因为返回的数据`json`格式问题. 调整格式即可.


### 报错信息:
```
Module not found: Error: Can't resolve 'crypto' in '
```

错误现象: 无法打开页面

解决方法: 删除`node`库, 重新安装, 操作步骤:
```node
npm install rimraf -g //安装删除指令
rimraf node_modules //卸载node库
npm install
```
据说原因是使用`cnpm`安装的库有问题, 最好还是用`npm`安装吧