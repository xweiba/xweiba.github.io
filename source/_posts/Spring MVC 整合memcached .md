---
title: SpringMVC 整合 memcached 
date: 2018-05-19 12:40:33
tags:
 - SpringMVC
 - memcached
 - 缓存
categories:
 - SpringMVC
---

## 今天完成的事情
- 完成Spring MVC memcachede 融合

## 整合
1. memcachede只缓存数据, 所以我们只需要在`service 层` 的实现中添加对应的缓存即可. 
- 原接口添加缓存
```java
@Override
public UserCustom findUserById(Integer id) throws Exception {
    // 查找缓存
    Object object = MemcacheUtils.get("user" + id);
    // 当存在缓存时直接返回缓存数据
    if (object != null) {
        return (UserCustom) object;
    }
    UserCustom userCustom = userDao.findUserById(id);
    // 当缓存为空时 添加 memcached 缓存
    MemcacheUtils.set("user" + id, userCustom);
    return userCustom;
}

@Override
public int insertUser(User user) throws Exception {
    //插入成功后返回的值存入了user的id中
    userDao.insertUser(user);
    // 写入缓存 这里使用add 当 key(id)存在时, 不写入缓存
    MemcacheUtils.add("user" + user.getId(), user);
    //所以返回user的id值
    return user.getId();
}

@Override
public boolean updateUser(UserCustom userCustom, Integer id) throws Exception {
    userCustom.setId(id);
    // 写入缓存 这里使用replace, 当key(id)不存在时, 不写入缓存
    MemcacheUtils.replace("user" + id, userCustom);
    return userDao.updateUser(userCustom);
}

@Override
public boolean deleteUser(Integer i) throws Exception {
    // 删除缓存
    MemcacheUtils.delete(String.valueOf(i));
    return userDao.deleteUser(i);
}

@Override
public boolean findAuth(Auth auth) throws Exception {
    // 密码验证的就不做缓存了
    return authDao.findAuth(auth);
}

```
- 测试 查找数据, 第一次从数据库中查询并将值存入到缓存, 第二次直接从缓存获取
![image](https://note.youdao.com/yws/public/resource/57891bc9ca8b961d3711858d81d17913/xmlnote/3DD4B0570A2F47CDA27DCE30C62B313D/1001)
![image](https://note.youdao.com/yws/public/resource/57891bc9ca8b961d3711858d81d17913/xmlnote/BFBB823ED53C482A94FF9C98EC053879/988)
![image](https://note.youdao.com/yws/public/resource/57891bc9ca8b961d3711858d81d17913/xmlnote/3747942646F746AF8B817A68CE94A50C/997)
- 测试 更新数据, 再次获取, 直接从缓存中获取更新过的数据
![image](https://note.youdao.com/yws/public/resource/57891bc9ca8b961d3711858d81d17913/xmlnote/4506D4F0AF334374A3549A152CC81C3E/1007)
![image](https://note.youdao.com/yws/public/resource/57891bc9ca8b961d3711858d81d17913/xmlnote/BFBD790007F0485AABE2EC5AD25DF931/1013)

2. 创建缓存api接口 
```java
package com.jnshu.controller;

import com.jnshu.model.UserCustom;
import com.jnshu.service.UserService;
import com.jnshu.tools.MemcacheUtils;
import com.whalin.MemCached.MemCachedClient;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.util.StringUtils;
import org.springframework.web.bind.annotation.*;


/**
 * @program: taskTwo
 * @description: MemCache 缓存接口
 * @author: Mr.xweiba
 * @create: 2018-05-19 00:06
 **/

@Controller
@RequestMapping("/memcache")
public class MemCacheController {
    @Autowired
    UserService userService;
    private static Logger logger = LoggerFactory.getLogger(MemCacheController.class);

    /**
    * @Description: 获取key为id的user缓存
    * @Param: [key]
    * @return: java.lang.Object
    * @Author: Mr.Wang
    * @Date: 2018/5/19
    */
    @RequestMapping(value = "/api/{id}", method = RequestMethod.GET)
    @ResponseBody
    public Object findByKey(@RequestBody @PathVariable("id") String key){
        if(StringUtils.isEmpty(key)){
            return "key must not be empty or null!";
        }
        return MemcacheUtils.get("user" + key);
    }

    /**
    * @Description: 缓存更新接口 当key不存在时取消更新
    * @Param: [key, userCustom] 键. 值
    * @return: boolean
    * @Author: Mr.Wang
    * @Date: 2018/5/19
    */
    @RequestMapping(value = "/api/{id}", method = RequestMethod.POST, produces = "application/json; charset=utf-8")
    @ResponseBody
    public boolean updateByKey(@PathVariable("id") String key, @RequestBody UserCustom userCustom){
        userCustom.setId(Integer.valueOf(key));
        if(StringUtils.isEmpty(key)){
            return false;
        }
        return MemcacheUtils.replace("user" + key, userCustom);
    }
    
    /** 
    * @Description: 增加缓存数据 当键存在时取消存入
    * @Param: [key, userCustom] 键, 值 
    * @return: java.lang.Boolean 
    * @Author: Mr.Wang 
    * @Date: 2018/5/19 
    */
    @RequestMapping(value = "/api/{id}", method = RequestMethod.PUT, produces = "application/json; charset=utf-8")
    @ResponseBody
    public Boolean insert(@PathVariable("id") String key, @RequestBody UserCustom userCustom){
        System.out.println(userCustom.toString());
        userCustom.setId(Integer.valueOf(key));
        if(StringUtils.isEmpty(key)){
            return false;
        }
        return MemcacheUtils.add("user" + key, userCustom);
    }
    
    /** 
    * @Description: 删除指定key 
    * @Param: [key] 
    * @return: java.lang.Boolean 
    * @Author: Mr.Wang 
    * @Date: 2018/5/19 
    */ 
    @RequestMapping(value = "/api/{id}", method = RequestMethod.DELETE)
    @ResponseBody
    public Boolean deleteByKey(@PathVariable("id") String key){
        if(StringUtils.isEmpty(key)){
            return false;
        }
        return MemcacheUtils.delete("user" + key);
    }
    /**
     * @Description: 清除缓存中的所有键值对
     * @Param: []
     * @return: boolean
     * @Author: Mr.Wang
     * @Date: 2018/5/19
     */
    @RequestMapping(value = "/api/all", method = RequestMethod.DELETE)
    @ResponseBody
    public boolean flashAll() throws Exception {
        return MemcacheUtils.flashAll();
    }
}

```
- 测试
- 
## 错误
1. 中文json数据插入后乱码
- 接收方法添加接收字符类型  `produces = "application/json; charset=utf-8"`
```java
@RequestMapping(value = "/api/{id}", method = RequestMethod.PUT, produces = "application/json; charset=utf-8")
    @ResponseBody
    public Boolean insert(@PathVariable("id") String key, @RequestBody UserCustom userCustom){
        System.out.println(userCustom.toString());
        userCustom.setId(Integer.valueOf(key));
        if(StringUtils.isEmpty(key)){
            return false;
        }
        return MemcacheUtils.add("user" + key, userCustom);
    }
```

2. 存储`对象`时, 报错: exception thrown while writing bytes to server on set
- 原因: memcachede接收的对象,必须序列化, 将存储的实体类实现`Serializable`接口即可
```java
public class User implements Serializable {
    ...
}
```
3. 将对象集合存入缓存, 取值时为空, 应该时集合序列化的问题, 还未解决

## 明日计划
- 解决错误3 
- Nginx 负载测试 