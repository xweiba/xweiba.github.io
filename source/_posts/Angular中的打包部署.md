---
title: Angular中的打包部署
date: 2020-11-23 13:55:58
tags:
 - Angular
 - 打包
 - 部署
categories:
 - Angular
---

## <center>13.Angular中的打包部署</center>
#### 1.打包指令
```
    ng build --base-href xx --prod
    
    --base-href:指定在生成和识别URL时应保留的URL前缀。
    
    --prod: 指定生产模式,会压缩文件并去掉代码中的console
```


#### 2.实例
```
    # /lv/ 是nginx中监听的path
	- 打包,并指定前缀
	ng build --base-href /lv/  --aot --prod
 
    - nginx中配置 location需要与ng打包的前缀一致
	location ^~ /lv {
		alias   D:/IdeaProject/ETS-Cloud_LiveService/front/live-manager/dist/live-manager; # 这是angular生成的dist文件夹存放的位置
		index  index.html index.htm;
		try_files $uri $uri/ /index.html =404; # 注意此句，一定要加上。否则配置的子路由等无法使用
	}
	
	
	# 配置webSocket连接
    location ^~ /live/websocket {
        proxy_pass http://live-microservice;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        client_max_body_size 1000m;
        proxy_set_header Upgrade $http_upgrade;  #ws配置
        proxy_set_header Connection "upgrade";   #ws配置
    }

```


### 3.angular中编码方式获取APP-BASE-HREF
> 1.第一种方式,通过index.html中的<base href="" />的值,来动态获取
```
/**
 * This function is used internal to get a string instance of the `<base href="" />` value from `index.html`.
 * This is an exported function, instead of a private function or inline lambda, to prevent this error:
 *
 * `Error encountered resolving symbol values statically.`
 * `Function calls are not supported.`
 * `Consider replacing the function or lambda with a reference to an exported function.`
 *
 * @param platformLocation an Angular service used to interact with a browser's URL
 * @return a string instance of the `<base href="" />` value from `index.html`
 */
export function getBaseHref(platformLocation: PlatformLocation): string {
    return platformLocation.getBaseHrefFromDOM();
}

@NgModule({
    declarations: [
        AppComponent
    ],
    providers: [
        {
            provide: APP_BASE_HREF,
            useFactory: getBaseHref,
            deps: [PlatformLocation]
        }
    ],
    bootstrap: [
        AppComponent
    ]
})
export class AppModule { }


constructor(@Inject(APP_BASE_HREF) private baseHref: string) {
    console.log(`APP_BASE_HREF is ${this.baseHref}`);
}
```


> 2. 通过指定LocationStrategy,通过Angular中LocationStrategy.getBaseHref()来获取
```
...
import { LocationStrategy, PathLocationStrategy } from '@angular/common';
...
@NgModule({
  providers: [
    ...
    {provide: LocationStrategy, useClass: PathLocationStrategy},
    ...
  ],
  ...
})
export class AppModule { }


constructor(private locationStrategy: LocationStrategy, ...) {
  console.log(this.locationStrategy.getBaseHref());
  ...
}
```


#### 4.集成https
```
    server {
		listen 8886 ssl;
        server_name  xxx.com;
        index index.html index.php;

        ssl_certificate      /etc/ssl/hwlive/4464505_hwlive.enable-ets.com.pem;
        ssl_certificate_key  /etc/ssl/hwlive/4464505_hwlive.enable-ets.com.key;

        ssl_session_cache    shared:SSL:1m;
        ssl_session_timeout  5m;

        ssl_ciphers  ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
        ssl_prefer_server_ciphers  on;
		
		location ^~ /live/websocket {
	        proxy_pass http://live-microservice;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            client_max_body_size 1000m;
            proxy_set_header Upgrade $http_upgrade;
			proxy_set_header Connection "upgrade";
         }
		 
		 
	    location /lv {
            alias /home/icampus3.0/pkgs/live-microservice/dist/live-manager;
    		index index.html index.htm;
    		try_files $uri $uri/ /index.html =404; 
        }
	}
```