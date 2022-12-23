---
title: Angular基础
date: 2020-11-23 13:51:28
tags:
 - Angular
categories:
 - Angular
---

## <center>1.Angular基础</center>
#### 1. Angular环境搭建
    1. 安装node.js
    2. 安装cnpm  
        npm install -g cnpm --registry=https://registry.npm.taobao.org
    3. 安装Angular脚手架
       npm install -g @angular/cli     或者    cnpm install -g @angular/cli

#### 2. Angular创建项目
    1. 使用 ng new 项目名称,来创建Angular
        ng new angulardemo
     跳过npmi安装,请使用
        ng new angulardemo --skip-install
    
    2. 运行项目
        ng serve --open

#### 3. Angular项目目录文件介绍
    1. e2e : 端对端测试文件
    2. node_modules : 依赖库,存放的依赖文件
    3. src : 源代码(主要开发部分)
    4. angular.json : angular的配置文件
    5. package.json : 项目的配置文件
    6. tsconfig.json和tslint.json : ts的配置文件

#### 4. src目录详解
    1. app : 存放组件和根模块目录
    2. assets : 存放静态资源
    3. browserslist : 支持浏览器配置文件
    4. index.html : html入口文件
    5. karma.conf.js : 端对端测试文件
    6. main.js : 整个项目的入口
    7.style.scss : 全局的样式文件

#### 5. app目录详解
    1. app.module.ts : Angular根摸快,告诉浏览器如何组装应用  
        BrowserModule:浏览器解析摸快  
        NgModule:Angular核心模块
        AppComponent:根组件
        @NgModule:是一个装饰器,接受一个元数据对象,告诉Angular如何编译以及启动
            declarations: 配置当前项目运行所需要的组件
            imports: 配置当前项目运行所依赖的其他模块
            providers: 配置项目所需要的服务
            bootstarp: 指定应用主视图,一般放根组件
            
    2. app.component.ts :组件定义文件
        @Component: 组件装饰器
            selector: 组件名称
            templateUrl: 组件使用的html
            styleUrls: 组建使用的css

#### 6. 组件
    1. 创建组件,使用angular-cli
        ng g component 目录/组件名
        通过此命令创建的组件,会在app.module.ts中import引入此组件,并且在@NgModule的declarations中添加