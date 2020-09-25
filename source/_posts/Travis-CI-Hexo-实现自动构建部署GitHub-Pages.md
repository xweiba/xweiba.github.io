---
title: Travis CI + Hexo 实现自动构建部署GitHub Pages
date: 2020-09-25 10:22:02
tags:

---



> 参考[Travis CI 加 Hexo 实现自动构建部署 Github Pages 博客](https://segmentfault.com/a/1190000021987832) 配置

需要的工具: 
- Git 客户端
- node npm

请先 `cd` 至创建项目的目录

# 安装 Hexo
```
npm install -g hexo-cli
```

# Hexo 配置

## 初始化 Hexo 站点

```
hexo init username.github.io
```
username 为你的github账号名

## Hexo 必要的一些修改

1. 用编辑器打开站点根目录的 `_config.yml`
2. 修改下面这些项目

| 项目                      | 解释                                                     | 建议值                                      |
| ------------------------- | -------------------------------------------------------- | ------------------------------------------- |
| title                     | 网站的标题                                               | 站点名, 可以例如 Weiba's Blog               |
| author                    | 你的名字                                                 | xweiba                                      |
| language                  | 网站的语言                                               | zh-CN 按地区                                |
| timezone                  | 网站的时区                                               | Asia/Shanghai 按地区                        |
| url                       | 网站的链接                                               | 如果你没有域名的话，就填 username.github.io |
| pretty_urls.trailing_html | 尾随.html，设置为false将其删除（不适用于尾随index.html） | false                                       |

其他的修改可以参考 Hexo 的文档: [Configuration | Hexo Doc](https://hexo.io/docs/configuration)

## 修改主题
1. 安装主题
```
cd themes
git clone https://github.com/SukkaW/hexo-theme-suka.git suka
cd suka
npm install --production
cp -i _config.example.yml _config.yml
```
> 本文以 [suka](https://github.com/SukkaW/hexo-theme-suka) 为例

2. 启用主题
```
cd ..        # 回到站点的根目录
cat themes/suka/site_config.yml >> _config.yml # 将主题配置追加至Hexo配置文件
```
打开站点根目录的 `_config.yml`, 将 `theme: landscape` 改为 `theme: suka`

改好之后，你站点的 `_config.yml` 大概是这样
```
# Extensions
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
theme: suka

# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: ''

# Suka-theme
suka_theme:
  search:
    enable: true
    path: search.json
    field: post # Page | Post | All. Default post
  prism:
    enable: false
    line_number: true
    theme: default
```

## 本地调试
```
hexo s --debug
```

# 将 Hexo 文件同步至 Github

## 初始化 Git

由于我是在 `CI` 阶段从网络直接 `clone` 整个主题，所以我就将 `themes/` 加入了 `.gitignore`，所以这里你还要将主题的配置文件复制到站点根目录内，之后 `CI` 阶段再将其放回原位置。(如果你用的主题有其他的配置文件时应该一并复制到站点根目录)

1. 复制一份主题的配置文件
```
cp themes/suka/_config.yml  _config.theme.yml
```

2. 初始化 `git`
```
git init
```

确保 `.gitignore` 内有下面的内容 (如果没有这个文件的话可以自己创建)
```
.DS_Store
Thumbs.db
db.json
*.log
node_modules/
public/
.deploy*/
themes/
```

3. 在 `Github` 新建一个命名为 `username.github.io` 的 `repository` (`username` 是你的 `Github` 账户名称)

4. 将 `Hexo` 推送至 `Github` 仓库
```
# 添加 Github 仓库到本地
git remote add origin https://github.com/xweiba/xweiba.github.io.git

# 新建一个名为 source 的分支
git checkout -b source

# 将所有文件添加到 git
git add .

# 添加 commit
git commit -m "initial"

# 将本地的文件推送到 Github 上的 source 分支
git push -u origin source
```

如果操作上没有问题你上传之后 `repository` 里面的文件应该差不多是这样的
```
.
├── _config.theme.yml
├── _config.yml
├── .gitignore
├── package.json
├── package-lock.json
├── scaffolds
└── source
```

# 配置 Travis CI
1. 将 [Travis CI](https://github.com/marketplace/travis-ci) 添加到你的 `Github` 账户

2. 前往 Github 的 [Applications settings](https://github.com/settings/installations)， 配置 `Travis CI` 的权限使其能够访问你的 `repository`

3. 前往 Github 的 [Personal Access Tokens](https://github.com/settings/tokens) 为 `Travis CI` 生成一个 `Token` ( 只需要 `repo` 这个权限(`scopes`) )，然后把 `Token` 的值记录下来

4. 前往 [Travis CI](https://travis-ci.com/)，在你的 `repository` 页面下点击 `More Options` 找到 `Settings`， 然后找到 `Environment Variables`，建立一个名( `NAME` )为 `GH_TOKEN`, 值( `VALUE` )为你上一步记录的 `Token`，最后保存(确保 `DISPLAY VALUE IN BUILD LOG` 保持 关闭 避免你的 `Token` 泄漏

在你的 `Github` 的项目 `source` 分支内新建一个名为 `.travis.yml` 的文件，参考以下内容进行填入。
```
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
  - git clone https://github.com/SukkaW/hexo-theme-suka.git suka #从 Github 上拉取 Suka 主题
  - cd suka
  - npm install --production # 安装 Suka 主题的依赖
  - cd ../.. # 返回站点根目录
  - cp _config.theme.yml themes/suka/_config.yml # 将主题的配置文件放回原处    
  - npm install # 在根目录安装站点需要的依赖 
script: 
  - hexo generate # generate static files
deploy: # 根据个人情况，这里会有所不同
  provider: pages
  skip_cleanup: true # 构建完成后不清除
  token: $GH_TOKEN # 你刚刚设置的 Token
  keep_history: true # 保存历史
  fqdn: blog.haukeng.me # 自定义域名，使用 username.github.io 可删除
  on:
    branch: source # hexo 站点源文件所在的 branch
  local_dir: public 
  target_branch: master # 存放生成站点文件的 branch，使用 username.github.io 必须是 master
```

6. 当你保存之后， `Travis CI` 便会开始部署， 它完成之后，你就可以在你的 `repo` 里 `master` 分支查看到生成的站点文件

7. 这时你应该就可以访问 [https://username.github.io](https://xweiba.github.io) 查看你的站点了.