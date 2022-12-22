---
title: xx视频APP API破解
date: 2022-12-22 18:51:51
tags: 
 - Android
 - 破解
 - API
 - 视频 
categories:
 - 反编译
---

> xx视频是一个在线观看视频的APP, 速度非常快, 清晰度也够看, 就是有点瑟瑟的广告, 今天闲着没事我瞅瞅他的api, 看能不能白嫖一波.

# Android HttpCanary 抓包. 
配置好 HttpCanary 后直接抓xx视频的包, 可以获取到 API 的请求情况, 但是他的 response 是经过加密的. 

![image-20221222194502744](XX%E8%A7%86%E9%A2%91API%E7%A0%B4%E8%A7%A3/image-20221222194502744.png)

![image-20221222185610423](XX%E8%A7%86%E9%A2%91API%E7%A0%B4%E8%A7%A3/image-20221222185610423.png)

![image-20221222185549743](XX%E8%A7%86%E9%A2%91API%E7%A0%B4%E8%A7%A3/image-20221222185549743.png)

那接下来只能通过反编译 APP 来看看能不能找到解析方法.

# 反编译APK

直接丢进 jadx-gui, 很幸运, 这个 APP 好像只做了混淆处理, 直接就成功了.

![image-20221222190714919](XX%E8%A7%86%E9%A2%91API%E7%A0%B4%E8%A7%A3/image-20221222190714919.png)

 ## 定位请求

全局搜索 `get_info`, 直接定位到 `BrowserApiService` 类, 通过这个类可以获取到所有的接口了.

![image-20221222190910113](XX%E8%A7%86%E9%A2%91API%E7%A0%B4%E8%A7%A3/image-20221222190910113.png)![image-20221222191429192](XX%E8%A7%86%E9%A2%91API%E7%A0%B4%E8%A7%A3/image-20221222191429192.png)

右键查找用例, 

![image-20221222191555696](XX%E8%A7%86%E9%A2%91API%E7%A0%B4%E8%A7%A3/image-20221222191555696.png)

![image-20221222191533818](XX%E8%A7%86%E9%A2%91API%E7%A0%B4%E8%A7%A3/image-20221222191533818.png)

好像看不出什么, 不过我看了下, 所有的接口返回的数据都是加密的, 所以估计是哪里统一做了处理. 

看下请求接口上有一个 `@FormUrlEncoded` 注解, 咱们来搜索一下看看是什么类库的即可进一步追踪. 

![image-20221222191742407](XX%E8%A7%86%E9%A2%91API%E7%A0%B4%E8%A7%A3/image-20221222191742407.png)

然后搜索 `retrofit 解密`

![image-20221222191806785](XX%E8%A7%86%E9%A2%91API%E7%A0%B4%E8%A7%A3/image-20221222191806785.png)

![image-20221222192518727](XX%E8%A7%86%E9%A2%91API%E7%A0%B4%E8%A7%A3/image-20221222192518727.png)

![image-20221222192526988](XX%E8%A7%86%E9%A2%91API%E7%A0%B4%E8%A7%A3/image-20221222192526988.png)

大概可以知道这个类库解密一般是通过添加 `Interceptor` 完成的. 接下来我们只需要找到这个 `Interceptor ` 就可以了. 

# 定位 Interceptor 

全局搜索 `.addInterceptor` , 这个是 `retrofit` 添加 `Interceptor` 的接口.

![image-20221222192305040](XX%E8%A7%86%E9%A2%91API%E7%A0%B4%E8%A7%A3/image-20221222192305040.png)

第一个就有收获了. 

![image-20221222192337862](XX%E8%A7%86%E9%A2%91API%E7%A0%B4%E8%A7%A3/image-20221222192337862.png)

```java
OkHttpClient.Builder addInterceptor = RetrofitUrlManager.getInstance().with(new OkHttpClient.Builder()).cookieJar(new CookieJarImpl(new PersistentCookieStore(f4030d))).addInterceptor(new CacheInterceptor(f4030d)).addInterceptor(new BaseInterceptor(map)).sslSocketFactory(b()).hostnameVerifier(new c(null)).addInterceptor(new a(this)).addInterceptor(new LoggingInterceptor.Builder().loggable(false).setLevel(Level.BASIC).log(4).request("Request").response("Response").addHeader("log-header", "I am the log request header.").build());
        TimeUnit timeUnit = TimeUnit.SECONDS;
        f4031e = addInterceptor.connectTimeout(10L, timeUnit).writeTimeout(10L, timeUnit).proxy(Proxy.NO_PROXY).connectionPool(new ConnectionPool(8, 15L, timeUnit)).build();
        f4032f = new Retrofit.Builder().client(f4031e).addConverterFactory(GsonConverterFactory.create()).addCallAdapterFactory(RxJava2CallAdapterFactory.create()).baseUrl(str).build();
```
# 查看解码实现
`response` 的处理一般看最后一个 `Interceptor`, 直接进去看实现, 找到 `chain.proceed(request)` 类似获取 `response` 的处理:

![image-20221222192742167](XX%E8%A7%86%E9%A2%91API%E7%A0%B4%E8%A7%A3/image-20221222192742167.png)

看上去这个 `c2` 就是我们需要的解码结果, 点进去看看.

![image-20221222193452741](XX%E8%A7%86%E9%A2%91API%E7%A0%B4%E8%A7%A3/image-20221222193452741.png)

![image-20221222193500470](XX%E8%A7%86%E9%A2%91API%E7%A0%B4%E8%A7%A3/image-20221222193500470.png)

![image-20221222193509855](XX%E8%A7%86%E9%A2%91API%E7%A0%B4%E8%A7%A3/image-20221222193509855.png)

# Java 复现测试
还好没有像智能公交那样使用 `lib` 库,  直接`java copy` 一下 `ok` 啦 ~~ 

![image-20221222194352182](XX%E8%A7%86%E9%A2%91API%E7%A0%B4%E8%A7%A3/image-20221222194352182.png)

![image-20221222194402480](XX%E8%A7%86%E9%A2%91API%E7%A0%B4%E8%A7%A3/image-20221222194402480.png) 

咱们`PP-Utils` 又添加一个新能力了, 嘿嘿 

接下来就是复刻他的请求了~~~

