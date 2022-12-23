---
title: Angular中的拦截器
date: 2020-11-23 13:54:32
tags:
 - 拦截器
 - Angular
categories:
 - Angular
---

## <center>10.Angular中的拦截器</center>

#### 1. 拦截器
```
    1.需要实现HttpInterceptor接口
    @Injectable()
    export class SignInterceptor implements HttpInterceptor {
        intercept(request: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>>{
            ...
        }
    }
    
    
    2.暴露拦截器链
    import { HTTP_INTERCEPTORS } from '@angular/common/http';

    import {SignInterceptor} from './sign.interceptor';
    
    //multi:true 表示有多个拦截器
    export const httpInterceptorProviders = [
      {provide: HTTP_INTERCEPTORS, useClass: SignInterceptor, multi: true},
    ];
    
    3.在模块中使用拦截器
    @NgModule({
      declarations: [...],
      imports: [...],
      providers: [httpInterceptorProviders]
    })
    
```