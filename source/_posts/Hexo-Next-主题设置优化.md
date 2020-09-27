---
title: Hexo Next 主题设置优化
date: 2020-09-25 14:36:48
tags: 
 - Hexo 
 - Next主题
---

>  `next` 主题相当有名, 不过默认配置看起来略微单调. 搜索了一下, 觉得这篇  [使用 Github 空间搭建 Hexo 技术博客——使用NexT优化博客](https://blog.csdn.net/wugenqiang/article/details/88373990)  相当不错, 记录一下.

可能是版本不同, 还是略微有点差异的.

<!--more-->





# 主题配置

已下修改不做说明时都为 `theme/next/-config.yml` 文件, 直接用`vim`或其他编辑器修改, 保存即可生效, 不用重启 `hexo`

## 开启需要的导航栏

在配置中找到 `menu` 看看自己博客所需的导航栏, 需要开启的将头部 `#` 号删除即可.

```yml
menu:
  home: / || fa fa-home # 首页
  about: /about/ || fa fa-user # 关于
  tags: /tags/ || fa fa-tags # 标签
  categories: /categories/ || fa fa-th # 分类
  archives: /archives/ || fa fa-archive # 归档
  #schedule: /schedule/ || fa fa-calendar
  #sitemap: /sitemap.xml || fa fa-sitemap
  #commonweal: /404/ || fa fa-heartbeat        
```

显示如下:

![image-20200925144839778](Hexo-Next-%E4%B8%BB%E9%A2%98%E8%AE%BE%E7%BD%AE%E4%BC%98%E5%8C%96/image-20200925144839778.png)

`menu_settings` 配置控制是否显示导航栏的图标和对应的文章数量

```yml
menu_settings:
  icons: true # 是否显示图标
  badges: false # 是否显示文章数量
```

## 创建导航栏对应的页面

命令行窗口进入项目目录执行

```shell
$ hexo new page about   #看看menu上还有什么标签没创建就行创建
$ hexo new page tags    #创建标签
$ hexo new page categories #创建分类
$ hexo new page achives #创建归档
```

创建完成之后我们在自己项目查找，如我的是 `myblog/source/` 目录下查看新创建好的相关标签页面，里面包含各自的`index.md`文件，大家可以自行编辑了。

## Schemes方案设置

页面结构修改:

```yml
# Schemes
#scheme: Muse  #这是 Nex默认版本，黑白主调，大量留白
#scheme: Mist  #Muse 的紧凑版本，整洁有序的单栏外观
#scheme: Pisces #双栏 Scheme，小家碧玉似的清新
scheme: Gemini  #双子座，也是双栏形式，和Pisces类似
```

![image-20200925150316977](Hexo-Next-%E4%B8%BB%E9%A2%98%E8%AE%BE%E7%BD%AE%E4%BC%98%E5%8C%96/image-20200925150316977.png)

## Social 修改

```yml
social:
  GitHub: https://github.com/xweiba || fab fa-github
  E-Mail: mailto:xiaoweiba1028@gmail.com || fa fa-envelope
  #Weibo: https://weibo.com/yourname || fab fa-weibo
  #Google: https://plus.google.com/yourname || fab fa-google
  #Twitter: https://twitter.com/yourname || fab fa-twitter
  #FB Page: https://www.facebook.com/yourname || fab fa-facebook
  #StackOverflow: https://stackoverflow.com/yourname || fab fa-stack-overflow
  #YouTube: https://youtube.com/yourname || fab fa-youtube
  #Instagram: https://instagram.com/yourname || fab fa-instagram
  #Skype: skype:yourname?call|chat || fab fa-skype

social_icons:
  enable: true # 开启
  icons_only: true # 只显示图标 不显示名称
  transition: true
```

![image-20200925151720389](Hexo-Next-%E4%B8%BB%E9%A2%98%E8%AE%BE%E7%BD%AE%E4%BC%98%E5%8C%96/image-20200925151720389.png)

## 头像设置

```yml
avatar:
  # Replace the default image and set the url here.
  url: https://avatars1.githubusercontent.com/u/24520686?s=400&u=78b21dec789dfb9c51379f11ae2722672546e3a6&v=4 #头像图片路径 图片放置在next/source/images
  # If true, the avatar will be dispalyed in circle.
  rounded: false #是否显示圆形头像，true表示圆形，false默认
  # If true, the avatar will be rotated with the cursor.
  rotated: false #是否旋转 true表示旋转，false默认
  opacity: 0.7  #透明度0~1之间
```

![image-20200925152121252](Hexo-Next-%E4%B8%BB%E9%A2%98%E8%AE%BE%E7%BD%AE%E4%BC%98%E5%8C%96/image-20200925152121252.png)

## toc边栏中的目录设置

```yml
toc:
  enable: true #是否启动侧边栏
  number: true  #自动将列表编号添加到toc。
  wrap: true #true时是当标题宽度很长时，自动换到下一行
  max_depth: 6 # 最大深度
```

![image-20200925152323973](Hexo-Next-%E4%B8%BB%E9%A2%98%E8%AE%BE%E7%BD%AE%E4%BC%98%E5%8C%96/image-20200925152323973.png)

## Creative Commons 4.0国际许可设置

![image-20200925152438727](Hexo-Next-%E4%B8%BB%E9%A2%98%E8%AE%BE%E7%BD%AE%E4%BC%98%E5%8C%96/image-20200925152438727.png)

![image-20200925152516868](Hexo-Next-%E4%B8%BB%E9%A2%98%E8%AE%BE%E7%BD%AE%E4%BC%98%E5%8C%96/image-20200925152516868.png)

## 开启自动摘录

**注意 `NexT 7.6.0` 及更高版本需要安装 `hexo-excerpt` 插件, 安装完毕需要重启 `hexo` 才会生效**

安装 `hexo-excerpt`: 

```shell
npm install hexo-excerpt --save
```

修改配置:

```yml
excerpt_description: true #是否自动摘录主页中的描述作为前导文本。
excerpt:
  depth: 1 # 摘录深度
  excerpt_excludes: []
  more_excludes: []
  hideWholePostExcerpts: true
```

## 记住位置

`save_scroll` 配置看 `git` 记录已经移动到插件里了, 暂时不知道怎么开启

## 代码块设置

```yml
codeblock:
  # Available values: normal | night | night eighties | night blue | night bright
  highlight_theme: galactic #代码高亮主题设置 设置喜欢的模式，默认：normal
  copy_button:
    enable: true  #是否添加复制按钮
    show_result: true  #是否显示文本复制结果
```

## 底部设置

```yml
footer:
  icon:
    name: user  #设置图标，想修改图标从https://fontawesome.com/v4.7.0/icons获取
    animated: true  #红心是否要为图标设置动画，默认为false 
    color: "#66CDAA"  #图标颜色
  beian:
    enable: false # 备案设置, 没有就不填吧
```

## 标签页图标

```yml
favicon:
  small: /images/favicon-16x16-next.png   #小图标 默认的NexT
  medium: /images/favicon-32x32-next.png  #中图标 默认NexT
  apple_touch_icon: /images/apple-touch-icon-next.png #苹果触摸图标
  safari_pinned_tab: /images/logo.svg   #safari固定标签
```

## 开启pjax

进入主题目录 `themes/next`, 获取`js`来安装依赖:

```shell
git clone https://github.com/theme-next/theme-next-pjax source/lib/pjax
```

开启配置:

```yml
pjax: true
```

# 主题样式优化

需在配置文件中开启, 注意这个是项目为根目录的, 并不是主题的根目录:

```
custom_file_path:
  #head: source/_data/head.swig
  #header: source/_data/header.swig
  #sidebar: source/_data/sidebar.swig
  #postMeta: source/_data/post-meta.swig
  #postBodyEnd: source/_data/post-body-end.swig
  #footer: source/_data/footer.swig
  #bodyEnd: source/_data/body-end.swig
  #variable: source/_data/variables.styl
  #mixin: source/_data/mixins.styl
  style: source/_data/styles.styl # 样式
```

## 主页文章添加阴影效果和圆角

打开 `source/_data/styles.styl` 向里面添加:

```
// 文章添加圆角
.post-block {
  padding: 40px;
  background: #fff;
  box-shadow: 0 2px 2px 0 rgba(0,0,0,0.12), 0 3px 1px -2px rgba(0,0,0,0.06), 0 1px 5px 0 rgba(0,0,0,0.12);
  border-radius: 0.5rem;
}
.post-block + .post-block {
  border-radius: 0.5rem;
}

// 文章下移10像素
.main-inner {
  margin-top: 10px;
}

// 导航栏 圆角 + 下移
.header-inner {
  border-radius: 0.5rem;
  margin-top: 10px;
}

// 个人信息圆角
.sidebar-inner {
  border-radius: 0.5rem;
}
```

## 安装背景动画模块

`NexT` 里面有几种动画背景样式 `canvas_nest` 、`three_waves`、`canvas_lines`、`canvas_sphere` 等.

我们安装 `canvas_nest`  这款看看, 进入主题目录`/themes/next` 执行:

```shell
git clone https://github.com/theme-next/theme-next-canvas-nest source/lib/canvas-nest
```

配置文件中添加:

```yml
canvas_nest: 
 enable: true
```

# Travis-CI 脚本修改

如有使用 `Travis-CI` , 需要重写下脚本~~

```yml
os: linux
language: node_js 
node_js:
  - 10  # 使用 nodejs LTS v10
branches:
  only:
    - source # 只监控 source 的 branch
before_script: ## 根据你所用的主题和自定义的不同，这里会有所不同
  - npm install -g hexo-cli # 在 CI 环境内安装 Hexo
  - mkdir themes # 由于我们没有将 themes/ 上传，所以我们需要新建一个
  - cd themes 
  - git clone https://github.com/theme-next/hexo-theme-next next #从 Github 上拉取 next 主题
  - cd next
  - npm install --production # 安装 next 主题的依赖
  - git clone https://github.com/theme-next/theme-next-pjax source/lib/pjax # 安装pjax
  - git clone https://github.com/theme-next/theme-next-canvas-nest source/lib/canvas-nest # 安装背景动画
  - cd ../.. # 返回站点根目录
  - cp _config.theme.yml themes/next/_config.yml # 将主题的配置文件放回原处    
  - npm install # 在根目录安装站点需要的依赖 
  - hexo new page about   #看看menu上还有什么标签没创建就行创建
  - hexo new page tags    #创建标签
  - hexo new page categories #创建分类
  - hexo new page achives #创建归档
  - npm install hexo-excerpt --save # 开启自动摘录
script: 
  - hexo generate # generate static files
deploy: # 根据个人情况，这里会有所不同
  provider: pages
  skip_cleanup: true # 构建完成后不清除
  token: $GH_TOKEN # 你刚刚设置的 Token
  keep_history: true # 保存历史
  #fqdn: blog.haukeng.me # 自定义域名，使用 username.github.io 可删除
  on:
    branch: source # hexo 站点源文件所在的 branch
  local_dir: public 
  target_branch: master # 存放生成站点文件的 branch，使用 username.github.io 必须是 master
```



