---
title: 将hexo自动构建部署从Travis CI迁移至GitHub Actions
date: 2022-02-28 09:31:34
tags:
 - hexo
 - Travis CI
 - GitHub Actions
categories:
 - hexo
 - GitHub Actions

---

> 很久没更新博客了，今天想更新一下发现构建失败了，看了下是因为 `Travis CI` 开始收费了，免费额度用完了，正好现在流行 `GitHub Actions`, 切换过来了. 没用过 ``GitHub Actions`, 这里使用的 `Workflow` 是基于 [使用 GitHub Actions 自动部署 Hexo 博客](https://printempw.github.io/use-github-actions-to-deploy-hexo-blog/) 的, 感谢大大.

仓库和 `Travis CI` 时一样，`master` 为部署分支，`source` 分支为博客构建分支，`blog` 都在这里面。

## Hexo 添加 Git 部署依赖

自动部署需要用到 `hexo-deployer-git` 依赖: `npm install -save hexo-deployer-git`.

编辑 `hexo` 配置:

```yaml
deploy: 
  type: git
  repo: git@github.com:xweiba/xweiba.github.io.git
  branch: master # 部署到的分支
  name: xweiba
  email: xiaoweiba1028@gmail.com
```

## 配置部署密钥

终端执行 `ssh-keygen -t rsa -C "xiaoweiba1028@gmail.com" -f D:\github\xweiba\hexo-deploy-key`, 即可生成 `id_rsa` 和 `id_rsa.pub` 文件。

将公钥 `hexo-deploy-key.pub` 设置为仓库的部署密钥 `Settings > Deploy keys`:

![image-20220228095534107](%E5%B0%86hexo%E8%87%AA%E5%8A%A8%E6%9E%84%E5%BB%BA%E9%83%A8%E7%BD%B2%E4%BB%8ETravis-CI%E8%BF%81%E7%A7%BB%E8%87%B3GitHub-Actions/image-20220228095534107.png)

然后在仓库的 `Settings > Actions` 中新增一个 `secret`，命名为 `DEPLOY_KEY`，把私钥 `hexo-deploy-key` 的内容复制进去，供后续使用。

![image-20220228095733027](%E5%B0%86hexo%E8%87%AA%E5%8A%A8%E6%9E%84%E5%BB%BA%E9%83%A8%E7%BD%B2%E4%BB%8ETravis-CI%E8%BF%81%E7%A7%BB%E8%87%B3GitHub-Actions/image-20220228095733027.png)

## 编写 Workflow

`Workflow` 就是 `GitHub Actions` 的配置文件，类似于 `.travis.yml`。

在 `source` 分支新建文件：

```bash
mkdir -p .github/workflows
touch .github/workflows/deploy.yml
```

编辑 `deploy.yml`：

```yaml
name: Hexo Deploy

# 只监听 source 分支的改动
on:
  push:
    branches:
      - source

# 自定义环境变量
env:
  POST_ASSET_IMAGE_CDN: true

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
      # 获取博客源码和主题
      - name: Checkout
        uses: actions/checkout@v2

      # 这里用的是 Node.js 14.x
      - name: Set up Node.js
        uses: actions/setup-node@v1
        with:
          node-version: '14'

      # 设置 yarn 缓存，npm 的话可以看 actions/cache@v2 的文档示例
      - name: Get yarn cache directory path
        id: yarn-cache-dir-path
        run: echo "::set-output name=dir::$(yarn cache dir)"

      - name: Use yarn cache
        uses: actions/cache@v2
        id: yarn-cache
        with:
          path: ${{ steps.yarn-cache-dir-path.outputs.dir }}
          key: ${{ runner.os }}-yarn-${{ hashFiles('**/yarn.lock') }}
          restore-keys: |
            ${{ runner.os }}-yarn-

      # 安装依赖
      - name: Install dependencies
        run: |
          yarn install --prefer-offline --frozen-lockfile

      # 从之前设置的 secret 获取部署私钥
      - name: Set up environment
        env:
          DEPLOY_KEY: ${{ secrets.DEPLOY_KEY }}
        run: |
          sudo timedatectl set-timezone "Asia/Shanghai"
          mkdir -p ~/.ssh
          echo "$DEPLOY_KEY" > ~/.ssh/id_rsa
          chmod 600 ~/.ssh/id_rsa
          ssh-keyscan github.com >> ~/.ssh/known_hosts
          git config --global user.name "xweiba"
          git config --global user.email "xiaoweiba1028@gmail.com"
      # 生成并部署
      - name: Deploy
        run: |
          npx hexo clean && npx hexo generate && npx hexo deploy
```

## 部署

只要 `source` 分支有修改就会触发仓库 `Actions` 的 `Workflows` 自动部署静态文件:

![image-20220228100502297](%E5%B0%86hexo%E8%87%AA%E5%8A%A8%E6%9E%84%E5%BB%BA%E9%83%A8%E7%BD%B2%E4%BB%8ETravis-CI%E8%BF%81%E7%A7%BB%E8%87%B3GitHub-Actions/image-20220228100502297.png)
