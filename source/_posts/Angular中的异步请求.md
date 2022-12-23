---
title: Angular中的异步请求
date: 2022-12-23 13:59:29
tags:
 - Angular
 - 异步请求
categories:
 - Angular
---

## <center>6.Angular中的异步请求</center>
#### 1. RxJs介绍
>  Angular中集成了RxJs来操作异步.
```
    常见的异步编程的几种方法:
        1. 回调函数
        2. 事件监听/发布订阅
        3. Promise(来自ES6)
        4. RxJs
        
    1.Promise使用demo:
        - Promise中resolve代表成功回调,reject代表失败回调
        funtion getPromiseData(){
            return new Promise((resolve,reject)=>{
                //通过resolve(data),传递结果
                setTimeout(()=>{
                    var name = '张三';
                    resolve(name);
                },1000);   
            });
        }
        
        - 外部获取Promise数据
        var promiseData = getPromiseData().then((data)->{
            console.log(data);
        })
        
    2.使用rxJs获取异步数据
        - 首先需要引入Observable
        import {Observable} from 'rxjs';
        
        - 通过observer.next(data),传递结果,observer.error(data)来处理异常
        function getRxJsData(){
            return new Observable((observer)->{
                setTimeout(()=>{
                    var name = '张三';
                    observer.next(name);
                },1000);
            });
        }         
        
        - 外部获取RxJs数据
        var rxJsData = getRxJsData().subscribe((data)->{
            console.log(data);
        });
```

#### 2. RxJs与Promise的区别
```
    RxJs比Promise功能更加强大
    1. RxJs可以通过unsubscribe(),来中止异步操作,而Promise不行
    2. RxJs执行的异步方法可以多次执行(如果异步方法是个定时循环的),而Promise不行
    3. RxJs还提供了很多便利的工具类(map,filter,类似java8)
        import {map,filter} from 'rxjs/operators';
        
        var stream = this.getRxJsData().pipe(
            filter((value)=>{
                if(value%2 ==0){
                    return true;
                }  
            }),
            map((value)=>{
                return value * value;
            })
        ).subscribe((data)=>{
            console.log(data);
        });
        

```

#### 3. Angular中数据交互
```
    1. 引入httpClientModule模块
        import {HttpClientModule} from '@angular/common/http';
        
    2. 依赖注入,在app.module.ts中注入模块
        @ngModule({
            ...
            imports:[
                BrowserModule,
                HttpClientModule
            ]
            ...
        })
        
    3. 组件内引入httpClient(作为一个服务引入,因此需要在构造方法内注入)
        import {HttpClient} from '@angular/common/http';
        
        export class xxx implements OnInit{
            constructor(public http:HttpClient){
                ...
            }
        }
        
    4. 请求数据
        - get请求: 
            this.http.get(apiUrl).subscribe((response)=>{
                ...    
            })
        
        - post请求: 
            需要设置请求头,因此需要额外引入HttpHeaders
            import {HttpClient,HttpHeaders} from '@angular/common/http';
            
            设置请求头
            const httpOptions = {headers: new HttpHeader({'content-Type':'application/json'})};
            
            请求数据
            this.post(apiUrl,data,httpOptions).subscribe((response)=>{
                ...    
            })
            
    5. 对jsonp的支持
        - 首先需要在app.module.ts中引入jsonp模块 HttpClientJsonpModule,并注入
            import {HttpClientModule,HttpClientJsonpModule} from '@angular/common/http';
            
            @ngModule({
            ...
            imports:[
                BrowserModule,
                HttpClientModule,
                HttpClientJsonpModule
            ]
            ...
        })
        
        - 使用jsonp(必须得后台支持)
            this.http.jsonp(api,'callback').subscribe((response)=>{
                ...
            })    
        
```