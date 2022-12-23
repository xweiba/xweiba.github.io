---
title: Spring 登陆验证 Session Cookie
date: 2018-05-21 12:40:33
tags:
 - Spring
 - Cookie
categories:
 - Spring
---

## 今日完成的事
- 线上项目 整合Cookie 登陆
- 通过 Memcached 来同步Session, 解决tomcat多开带来的Session同步问题.

## Memcached 同步 Session
1. 由于使用了多台tomcat, 在Nginx做负载均衡时会将请求随机分发到各个tomcat上, 当属于tomcat_1的登陆后会话请求被转发到tomcat_2上时, 就会出现登陆验证错误, 因为tomcat_2上这个会话并未登陆.

2. 解决方法, 通过Memcache保存Session会话.
   - 注意, Session是容器保存的,内容放置在服务器具体的文件中,并不是程序保存的, 这个概念一定要搞清楚.
    - 我这里场景是 登陆后执行一个请求列出所有用户, 所以不能直接转到jsp. 
    - 使用 `redirect` 跳转页面会导致 session 值丢失. [参考连接1](https://blog.csdn.net/qshpeng/article/details/51506729) [参考连接2](https://blog.csdn.net/zhanghongzheng3213/article/details/52415839)
    - 解决方法
      1. 思路: handler中保存session后,先forward到一个handler再redirect到目标handler, session值不会丢失
      2. 具体实现:
          - handler 中保存 session, 此时session还未保存到容器中
             - 返回页面使用redirect到新页面,因为重定向特性不会带前页面的数据, 所以容器会将session值直接丢弃,导致session值丢失.(session id还在)
             - 返回时使用 forward 到新handler, 容器会将session保存.
          - 在新handler中再redirect到新页面, 此时session已保存,不会影响
   
### 将Tomcat的session由Memcached保存  准备工作
- Nginx 负载均衡已配置
- Tomcat 已开启多个容器
- Tomcat 8
- 官方文档: `https://github.com/magro/memcached-session-manager/wiki/SetupAndConfiguration`
- 参考资料: `https://www.cnblogs.com/jsonhc/p/7344902.html`
- 
### 下载所需jar包, 每个tomcat版本对应的包不同
- `wget http://repo1.maven.org/maven2/de/javakaffee/msm/memcached-session-manager/2.1.1/memcached-session-manager-2.1.1.jar`
- `wget http://repo1.maven.org/maven2/de/javakaffee/msm/memcached-session-manager-tc8/2.1.1/memcached-session-manager-tc8-2.1.1.jar`
- `wget http://repo1.maven.org/maven2/net/spy/spymemcached/2.11.1/spymemcached-2.11.1.jar`
- 如果仅仅只是用java来做序列化器只需上面这三个包就ok
- 下面两个包,配置后一直报错,未测试通过 
- `wget http://repo1.maven.org/maven2/de/javakaffee/msm/msm-javolution-serializer/1.8.3/msm-javolution-serializer-1.8.3.jar`
- `wget http://repo1.maven.org/maven2/de/javakaffee/msm/msm-javolution-serializer/2.1.1/msm-javolution-serializer-2.1.1.jar`
- 下载完成后放入tomcat/lib目录中

### 修改两个tomcat配置文件,启动
- vim `{tomcat}/conf/context.xml`

    ```sh
    <Context>
        <!-- web application will be reloaded.                                   -->
        <WatchedResource>WEB-INF/web.xml</WatchedResource>
        <WatchedResource>${catalina.base}/conf/web.xml</WatchedResource>
    
        <!-- Uncomment this to disable session persistence across Tomcat restarts -->
        <!--
        <Manager pathname="" />
        -->
        <Manager className="de.javakaffee.web.msm.MemcachedBackupSessionManager"
        memcachedNodes="n1:127.0.0.1:11211,n2:127.0.0.1:22122"
        failoverNodes="n1"
        requestUriIgnorePattern=".*\.(ico|png|gif|jpg|css|js)$"
        />
    </Context>
    ```

### 开一个test项目测试
- test/index.jsp, 分别写到两个 tomcat 测试, 修改{TomcatA}.liuhuan.ml代指Tomcat容器
```jsp
<%@ page language="java" %>
<html>
<head><title>TomcatA</title></head>
<body>
<h1><font color="blue">TomcatA.liuhuan.ml</font></h1>
<table align="centre" border="1">
<tr>
    <td>SessionID</td>
    <%session.setAttribute("linuxinfo.top","linuxinfo.top");%>
    <td><%=session.getId() %></td>
</tr>
<tr>
    <td>Createdon</td>
    <td><%=session.getCreationTime() %></td>
</tr>
</table>
</body>
</html>
```
### 测试
![image](Spring%20%E7%99%BB%E9%99%86%E9%AA%8C%E8%AF%81%20Session%20Cookie/2cffeaf1-9949-484a-bacf-8cdfff596573.png)
![image](Spring%20%E7%99%BB%E9%99%86%E9%AA%8C%E8%AF%81%20Session%20Cookie/e7e5416a-0c0e-4d54-ba61-071d7d8c0c55.png)

### 启动Memcached 
- `memcached -p 11211 -m 64m -vv -u root`
- `memcached -p 22122 -m 64m -vv -u root`

### 测试
```jsp
<%@ page language="java" %>
<html>
<head><title>TomcatA</title></head>
<body>
   <h1><font color="blue">TomcatA.liuhuan.ml</font></h1>
   <table align="centre" border="1">
       <tr>
           <td>SessionID</td>
           <%session.setAttribute("linuxinfo.top","linuxinfo.top");%>
<td><%=session.getId() %></td>
       </tr>
       <tr>
           <td>Createdon</td>
           <td><%=session.getCreationTime() %></td>
       </tr>
   </table>
</body>
</html>
```

### 实际项目
1. session 登陆验证 `Controller`
    ``` java
    //登陆
    @RequestMapping("/login.action")
    public String login(HttpSession session, Auth auth) throws Exception {
        if(userService.findAuth(auth)){
    
            // 在session中保存用户身份信息
            session.setAttribute("username", auth.getUsername());
            logger.info("session.getId():" + session.getId());
            // 存入缓存 用作效验
            MemcacheUtils.set(session.getId(), auth.getUsername());
        }
        //redirect 重定向到用户信息页面,会导致session内容丢失. 这里先forward保存session
        return "forward:/s/succed.action";
    }
    
    //退出登陆
    @RequestMapping("/logout.action")
    public String logout(HttpSession session){
        // 删除缓存
        MemcacheUtils.delete(session.getId());
        //删除session
        session.invalidate();
        return "redirect:/login.action";
    }
    ```
2. 拦截器效验
    ```
    //执行Handler方法之前执行
    //用于身份认证、身份授权
    //比如身份认证，如果认证通过表示当前用户没有登陆，需要此方法拦截不再向下执行
    @Override
    public boolean preHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o) throws Exception {
        //handler 开始时间
        this.timer = System.currentTimeMillis();
        //获取请求的url
        String url = httpServletRequest.getRequestURI();
        //判断url是否是公开地址(实际使用时将公开地址配置到配置文件中)
    
        //正则匹配url
        Pattern pattern = Pattern.compile("^.*login.action.*$|^.*rest\\/api\\/.*$|^.*home\\/.*$|^.*profession\\/.*$|^.*memcache\\/.*$");
        Matcher matcher = pattern.matcher(url);
    
        //可以导入一个配置文件,匹配其中的请求
        if (matcher.matches()){
            //如果要进行登陆提交,放行
            return true;
        }
    
        // 获取 session
        HttpSession session = httpServletRequest.getSession();
        // 从session中取出用户信息
        String username = (String)session.getAttribute("username");
        logger.debug("尝试登陆用户: " + username + "session_id:" + session.getId());
    
        // 登陆数据效验
        if(username !="" &&username!=null){
            String sessionName = (String) MemcacheUtils.get(session.getId());
            logger.info("登陆用户名: " + username +  ", sessionName:" + sessionName + ", session.getId():" + session.getId());
            if(username.equals(sessionName)){
                //身份存在 放行
                logger.debug("身份存在 放行");
                return true;
            }
        }else{
            // 当 session或缓存的 username 的 值为空时(可能是缓存出问题,又或者浏览器将seesion删除), 需要删除其session重新创建, 否则会一直判空
            // 删除缓存
            MemcacheUtils.delete(session.getId());
            //删除session
            session.invalidate();
        }
        //执行到这里标识用户身份需要认证,跳转到登陆界面
        //跳转网址需要绝对路径,将当前请求重新映射到/WEB-INF/jsp/login.jsp,
        // WEB-INF/jsp/login.jsp访问的是原地址+WEB-INF/jsp/login.jsp
        httpServletRequest.getRequestDispatcher("/WEB-INF/jsp/login.jsp").forward(httpServletRequest, httpServletResponse);
        logger.info("用户身份需要认证,跳转至登陆页面,执行Handler方法之前执行");
        //return false表示拦截，不向下执行 此时应计算页面结束时间
        this.timer = System.currentTimeMillis() - this.timer;
        logger.debug( "性能日志 页面生成时长 : " + this.timer + "ms" );
        //return true表示放行
        return false;
    }
    ```
### 登陆测试 
1. session 登陆测试
![image](Spring%20%E7%99%BB%E9%99%86%E9%AA%8C%E8%AF%81%20Session%20Cookie/59f903cf-1628-4ee9-ba9a-45177a98551a.png)
![image](Spring%20%E7%99%BB%E9%99%86%E9%AA%8C%E8%AF%81%20Session%20Cookie/9a0bcebe-2251-42e2-9e74-d5c075af201f.png)
    - 日志
![image](https://note.youdao.com/yws/public/resource/57891bc9ca8b961d3711858d81d17913/xmlnote/BF7C9D39EDE5456D97BB7342746DFE3A/1624)
![image](https://note.youdao.com/yws/public/resource/57891bc9ca8b961d3711858d81d17913/xmlnote/DA25E0FEE5364BBAA18A806C438C51B3/1626)
    - 重启tomcat, session任然可以登陆
![image](https://note.youdao.com/yws/public/resource/57891bc9ca8b961d3711858d81d17913/xmlnote/2557DD4864C34F599F636FEE686300AD/1635)
    - 刷新页面 正常显示
![image](https://note.youdao.com/yws/public/resource/57891bc9ca8b961d3711858d81d17913/xmlnote/1FFEB2DE5D464021AFEF6545B4247314/1640)
    - 日志
![image](https://note.youdao.com/yws/public/resource/57891bc9ca8b961d3711858d81d17913/xmlnote/363EC85C74D140ADB25D7890A8104CE0/1644)

- Cookie登陆, 由于它存储在用户端, 后端只需要解密验证, 没有同步问题, 服务器端重启也是不影响它验证的
![image](https://note.youdao.com/yws/public/resource/57891bc9ca8b961d3711858d81d17913/xmlnote/0F8AD413B7C74644ACA028A75D8BAD03/1663)
![image](https://note.youdao.com/yws/public/resource/57891bc9ca8b961d3711858d81d17913/xmlnote/D70D564D5F754C06B2CC87C9C06F1850/1665)

## 总结
- memcached 因为session同步的问题拖了一天, 今天就结束了, 相对cookie验证, 纯session相对限制多一些. 

## 明日计划
- 更换Redis测试. 