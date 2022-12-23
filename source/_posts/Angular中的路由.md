---
title: Angular中的路由
date: 2020-11-23 14:00:04
tags:
 - Angular
 - 路由
categories:
 - Angular
---

## <center>7.Angular中的路由</center>
#### 1. 路由
```
    - 路由: 即根据不同的url地址,动态的让根组件挂载其他组件,来实现一个单页面应用程序
    
    - 带路由模块的项目比起不带路由项目中:
        1. 在app目录下多一个app-routing.modules.ts,即路由的配置文件
        2. 在app.modules.ts中引入了路由模块,并注入
            import {AppRoutingModule} from './app-routing.module';
            
            @NgModule({
                ...
                imports:[
                    BrowserModule,
                    AppRoutingModule
                ]
                ...
            })
        3.在根组件的html中新增了标签
            <router-outlet></router-outlet>
    
```

#### 2. 路由的使用
```
    1. 需要在路由的配置文件app-routing.modules.ts中,引入使用路由的组件
        import {xxxComponent} from '../components/xxx';
        
    2. 配置路由
        const routes: Routes = [
            {path:'xxx',component:xxxComponenet},
            ...
            {path:'**',redirectTo:'xxxPath' | component:xxxComponenet}
        ]
        
        path: url地址
        component: 访问对应path,需要挂载的组件
        
        path:'**',即任意路由,一般用于匹配其他值或默认值(类似switch中的default)
        redirectTo: 跳转的路由(也可配置component,挂载对应的路由,一般还是跳转)
        
    3. 组件模板中使用路由(使用routerLink),并配置router-outlet来显示动态加载的路由组件视图
        // routerLink即路由path,可配置为动态(即使用 [] ),也可为静态
        <a [routeLink]='/home'>首页</a>
        <router-outlet></router-outlet>
        
    4. 路由选中状态
        //给路由的标签加上routerLinkActive属性,属性值即class名,在css样式文件中设置样式即可
        <a [routeLink]='/home' routerLinkActive="active">首页</a>
```

#### 3. 路由跳转传值
- 路由get传值
```
    // 例如 url=/xxx?aid=123
    1. 组件html中(前端)
    <a [routeLink]=['/home'] [queryParams]="{aid:123,xxx:xxx,...}">首页</a>
    
    2. 组件后台获取get传值
    //首先需要在组件后台引入ActivatedRoute
    import {ActivatedRoute} from '@angular/router';
    
    export class xxx implements OnInit{
        constructor( public route:ActivatedRoute){}
        
        ngOnInit(){
            //因为 this.route.queryParams 是一个RxJs对象,因此要通过subscribe()来获取
            this.route.queryParams.subscribe((data)=>{
                console.log(data.aid);
            })
        }
    }
```

- 动态路由
```
    // 例如 url=/xxx/123
    1. 需要修改路由的配置文件
    //其中:aid为动态值,类似于java中的pathParam
    const routes: Routes = [
        {path:'xxx/:aid',component:xxxComponenet},
    ]
    
    2. 组件html中(前端)
    //直接在routeLink中传递参数 
    <a [routeLink]=['/home',data]>首页</a>   
    
    3. 组件后台获取值
    //首先需要在组件后台引入ActivatedRoute
    import {ActivatedRoute} from '@angular/router';
    
    export class xxx implements OnInit{
        constructor( public route:ActivatedRoute){}
        
        ngOnInit(){
            //因为 this.route.params也是一个RxJs对象,因此同样通过subscribe()来获取
            this.route.params.subscribe((data)=>{
                console.log(data.aid);
            })
        }
    }
```

- 通过js跳转路由
```
    同样分为2种,动态路由js跳转,以及路由getJs跳转
    
    1. js-动态路由跳转
        - 需要引入Route模块
        import {Route} from '@angular/router';
        
        - 注入route,通过route.navigate()进行跳转
        export class xxx implements OnInit{
            constructor( public route:Route){}
            
            ngOnInit(){}
            
            goDetail(){
                this.route.navigate(['url',param]);
            }
        }
        
    2. js-get传值跳转
        - 需要引入NavigationExtras模块
        import {Route,NavigationExtras} from '@angular/router';
        
        - 定义queryParams
        let queryParams:NavigationExtras = {
            queryParams:{'aid':'xxx'}
        }
        
        this.route.navigate(['url'],queryParams);
        
        //注意,动态路由,传值写在[]内,get传值,写在[]外
        
```

#### 4. 父子路由(嵌套路由)
```
    只需要修改路由的配置文件,新增children属性
    const routes: Routes = [
            {
                path:'xxx',component:xxxComponenet
                children:[
                    {path:'xxx/children1',component:xxxComponenet1},
                    {path:'xxx/children2',component:xxxComponenet2},
                    {path:'xxx/**',redirectTo:'xxx/children1'}
                ]
            },
            ...
            {path:'**',redirectTo:'xxxPath' | component:xxxComponenet}
        ]
```