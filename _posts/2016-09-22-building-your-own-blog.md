---
layout:     post
title:      "Building your own blog"
subtitle:   " \"Getting started with Github Pages and Jekyll\""
date:       2016-09-22 22:55:00
author:     "Charkey"
header-img: "img/post-bg-2015.jpg"
tags:
    - Github Pages
    - Jekyll
    - Git
    - Markdown
---

> “Github Pages 是什么？ Jekyll 好用吗？”


## 关于 Github Pages

访问 [GitHub Pages](https://pages.github.com/) 按照页面的流程从上到下，可以很轻松的完成相关设置。

Github Pages 分为两种：

1. User or organization site: 是以你的用户名创建的仓库，如 *username*.github.io ,每个用户名下只能创建一个。只需要创建仓库、新增 html 文件（如 index.html）、add、commit、push，通过浏览器访问 http://*username*.github.io 即能看到页面出现。
2. Project site: 是依附项目的Pages。 你可以创建一个新的 repo 或者使用已有的 repo，在仓库的 **Settings** 中创建一个新的分支 `gh-pages`， Github Pages 会使用这个分支的内容来发布你的站点。

[Github Pages Basics](https://help.github.com/categories/github-pages-basics/)

---

## Jekyll 模版系统

[Jekyll](http://jekyllrb.com/) 

[博客](http://beiyuu.com/github-pages)

在 Ubuntu 上安装 Ruby 2.0.0 以上版本：

可以在 [Ruby 官网](https://www.ruby-lang.org/en/downloads/) 上 **Stable releases** 部分找到最新稳定版的源码下载地址

然后按照如下的命令，即可在 Ubuntu 上安装新版本 Ruby。


```shell
sudo apt-get -y update
sudo apt-get -y install build-essential zlib1g-dev libssl-dev libreadline6-dev libyaml-dev
cd /tmp
wget http://cache.ruby-lang.org/pub/ruby/2.0/ruby-2.0.0-p481.tar.gz
tar -xvzf ruby-2.0.0-p481.tar.gz
cd ruby-2.0.0-p481/
./configure --prefix=/usr/local
make
sudo make install
```

## 绑定域名

[如何绑定域名到 Github Pages](https://help.github.com/articles/about-supported-custom-domains/)