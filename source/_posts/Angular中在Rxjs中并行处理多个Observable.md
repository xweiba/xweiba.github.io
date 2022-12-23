---
title: Angular中在Rxjs中并行处理多个Observable
date: 2020-11-23 13:58:15
tags:
 - Angular
 - Rxjs
 - Observable
categories:
 - Angular
---

## <center>15.Rxjs中并行处理多个Observable</center>

#### 1. forkJoin
```
    let o1 = new Observable();
    let o2 = new Observable();
    let o3 = new Observable();

    forkJoin([o1,o2,o3]).subscribe([r1,r2,r3]=>{
        ...
    })
```
> forkJoin:当每个Observable都完成时,才会触发subscribe方法,即每个Observable对象内,都需要有observer.complete()

#### 2. combineLatest

```
    let o1 = new Observable();
    let o2 = new Observable();
    let o3 = new Observable();

    combineLatest([o1,o2,o3]).subscribe([r1,r2,r3]=>{
        ...
    })

```
> combineLatest:第一次,需要所有Observable都有返回值,后面只要某一个监听的Observable,返回值有更新,都会触发subscribe方法