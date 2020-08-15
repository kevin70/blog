---
title: "Github Actions & Hugo 自动更新博客"
date: 2020-08-15T11:35:21+08:00
tags: ["GitHub","GitHub Actions","Hugo"]
categories: ["其它"]
---

使用 [hugo](https://gohugo.io/) 搭建个人博客，[GitHub](https://github.com/) 管理博客资源，[GitHub Actions](https://github.com/features/actions) 自动构建更新博客站点。

## 前期准备
* GitHub
* Docker
* Nginx
* Hugo
* 一个已经备案的域名
* 一台在线的服务器
* Let's Encrypt HTTPS 证书

## GitHub Actions 自动发布

```yaml
name: Deploy Blog

on:
  push:
    branches: [ master ]
  pull_request:
    branches: [ master ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2

    - name: Deploy Blog
      uses: appleboy/ssh-action@master
      with:
        host: ${{secrets.BLOG_ALIYUN_HOST}}
        username: ${{secrets.BLOG_ALIYUN_USERNAME}}
        key: ${{secrets.ID_RSA_HUG_BLOG_ALIYUN}}
        script:  |
          if [ ! -d "kk70-blog-source" ]; then
            git clone https://github.com/kevin70/blog.git kk70-blog-source
          fi
          cd kk70-blog-source
          git pull --rebase
          git submodule update --init --recursive

          docker run --rm -i -v ~/kk70-blog-source:/src klakegg/hugo:0.74.3

          if [ ! -d "/usr/share/nginx/kk70.top" ]; then
            mkdir -p /usr/share/nginx/kk70.top
          else
            rm -rf /usr/share/nginx/kk70.top/*
          fi
          cp -R public/* /usr/share/nginx/kk70.top/
```

使用 [appleboy/ssh-action](https://github.com/appleboy/ssh-action) 连接远程服务器构建更新 Blog。

在 GitHub 仓库的 **Settings > Secrets** 中配置敏感参数。

1. 服务器主机
2. 服务器登录用户名
3. SSH 证书私钥

### 检出源码 shell
```shell
if [ ! -d "kk70-blog-source" ]; then
  git clone https://github.com/kevin70/blog.git kk70-blog-source
fi
cd kk70-blog-source
git pull --rebase
git submodule update --init --recursive
```

### 使用 docker 构建
```shell
docker run --rm -i -v ~/kk70-blog-source:/src klakegg/hugo:0.74.3
```

### 将站点部署到 Nginx
```shell
if [ ! -d "/usr/share/nginx/kk70.top" ]; then
  mkdir -p /usr/share/nginx/kk70.top
else
  rm -rf /usr/share/nginx/kk70.top/*
fi
cp -R public/* /usr/share/nginx/kk70.top/
```

---
## 结束