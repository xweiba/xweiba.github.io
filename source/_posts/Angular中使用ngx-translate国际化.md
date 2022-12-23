---
title: Angular中使用ngx-translate国际化
date: 2022-12-23 14:01:52
tags:
 - ngx-translate
 - Angular
categories:
 - Angular
---

## <center>12.Angular中使用ngx-translate国际化</center>
#### 1. 下载依赖
```
    npm install @ngx-translate/core --save
    
    -- 如果需要指定翻译文件,则还需下载
    npm install @ngx-translate/http-loader --save
    
```

#### 2. 导入TranslateModule模块
```
    import { TranslateLoader, TranslateModule } from '@ngx-translate/core';
    import { TranslateHttpLoader } from '@ngx-translate/http-loader';
    
    @NgModule({
      declarations: [
        AppComponent
      ],
      imports: [
        ...
        TranslateModule.forRoot({
          loader: {
            provide: TranslateLoader,
            useFactory:  (http: HttpClient) => new TranslateHttpLoader(http, './assets/i18n/', '.json'), //指定加载的配置文件
            deps: [HttpClient]
          },
          defaultLanguage: 'tw' //指定默认语系
        })
      ],
      providers: [
        { provide: MAT_DIALOG_DATA, useValue: {} }
      ],
      bootstrap: [AppComponent]
    })
```



#### 3. 使用
```
    在html中使用,可以通过指定或者管道
    <span (click)="editChannel()">{{'edit'|translate}}</span>
    
    //translate:params,params是参数,key与的翻译文件中的一致
    <span [innerHTML]="'liveChannel.pageTips'|translate:{'0':totalCount}"></span>
    
    
    在ts中使用,需要引入TranslateService
    import { TranslateService } from '@ngx-translate/core';
    
    constructor(public translate: TranslateService) {}
    
    //翻译多语言
    this.translate.instant('liveChannel.selectOneTip')
```

> [官网地址](https://github.com/ngx-translate/core)


#### 4. ts中使用`translate.instant`未初始化
在`AppModule`中添加:
```
providers: [{
    provide: APP_INITIALIZER,
    useFactory: appInitializerFactory,
    deps: [TranslateService, Injector],
    multi: true
  }]

export function appInitializerFactory(translate: TranslateService, injector: Injector) {
  return () => new Promise<any>((resolve: any) => {
    // console.log('appInitializerFactory--');
    const locationInitialized = injector.get(LOCATION_INITIALIZED, Promise.resolve(null));
    locationInitialized.then(() => {
      const langToSet = environment.locale;
      translate.setDefaultLang(environment.locale);
      translate.use(langToSet).subscribe(() => {
        // console.info(`Successfully initialized '${langToSet}' language.'`);
      }, err => {
        // console.error(`Problem with '${langToSet}' language initialization.'`);
      }, () => {
        resolve(null);
      });
    });
  });
}
```


```
@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    BrowserAnimationsModule,
    FormsModule,
    ReactiveFormsModule,
    HttpClientModule,
    TranslateModule.forRoot({
      loader: {
        provide: TranslateLoader,
        useFactory: (http: HttpClient) => new TranslateHttpLoader(http, './assets/i18n/', '.json'),
        deps: [HttpClient]
      },
      defaultLanguage: environment.locale
    }),
  ],
  providers: [{
    provide: APP_INITIALIZER,
    useFactory: appInitializerFactory,
    deps: [TranslateService, Injector],
    multi: true
  }],
  bootstrap: [AppComponent]
})
export class AppModule { }


export function appInitializerFactory(translate: TranslateService, injector: Injector) {
  return () => new Promise<any>((resolve: any) => {
    // console.log('appInitializerFactory--');
    const locationInitialized = injector.get(LOCATION_INITIALIZED, Promise.resolve(null));
    locationInitialized.then(() => {
      const langToSet = environment.locale;
      translate.setDefaultLang(environment.locale);
      translate.use(langToSet).subscribe(() => {
        // console.info(`Successfully initialized '${langToSet}' language.'`);
      }, err => {
        // console.error(`Problem with '${langToSet}' language initialization.'`);
      }, () => {
        resolve(null);
      });
    });
  });
}

  
```