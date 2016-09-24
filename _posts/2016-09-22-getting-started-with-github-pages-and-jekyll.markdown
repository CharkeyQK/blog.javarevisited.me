---
layout:     post
title:      "Getting started with Github Pages and Jekyll"
subtitle:   " \"Building your own blog\""
date:       2016-09-22 22:55:00
author:     "Charkey"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Github Pages
    - Jekyll
    - Git
    - Markdown
---

> “Github Pages 是什么？ Jekyll 好用吗？”

## 本文写什么？

如果想要搭建一个自己的博客系统，简单还是复杂？需要购买主机？搭建服务器环境？购买域名？

使用 Github Pages 配合静态博客系统很方便的就能完成博客的搭建，况且这货肯定比较稳定XD。

*GitHub Pages is a static site hosting service.* 

例如Jekyll，使用Markdown语法写博客，Jekyll将其转换为静态网页。

想要博客域名有个性？Github Pages 支持绑定自定义域名。

## 关于 Github Pages

Github Pages 分为两种：

1. User or organization site: 是以你的用户名创建的仓库，如 *username*.github.io ,每个用户名下只能创建一个。只需要创建仓库、新增 html 文件（如 index.html）、add、commit、push，通过浏览器访问 http://*username*.github.io 即能看到页面出现。
2. Project site: 是依附项目的Pages。 你可以创建一个新的 repo 或者使用已有的 repo，在仓库中创建一个新的分支 `gh-pages`， Github Pages 会使用这个分支的内容来发布你的站点。

入门知识阅读：[Github Pages Basics](https://help.github.com/categories/github-pages-basics/)

下面分别描述一下这两种 Github Pages 都是如何创建的。

我自己采用的是 Project site 的方式，因为我把博客仓库创建为博客域名了（blog.javarevisited.me），而不是默认的 *username*.github.io。

### 如何创建 User site

1. 在自己的账户下，以自己的用户名创建一个仓库。比如我这边的 *charkeyqk.github.io*
![Create a new repository](/img/in-post/building-your-own-blog/create-a-new-repository.jpg)

2. 克隆刚创建的仓库到本地
```shell
git clone https://github.com/CharkeyQK/charkeyqk.github.io.git
```

3. 进入项目目录
```shell
cd charkeyqk.github.io
```

4. 新建首页 *index.html*
```shell
echo hello world > index.html
```

5. 添加修改、提交并推送到 Github
```shell
git add .
git commit -m 'index'
git push
```

6. 访问 *https://charkeyqk.github.io* 即可看到 hello world
> Github Pages 如果使用默认域名是需要使用https才能访问的，而自定义域名是无法在这边启用https的。Enforce HTTPS — Required for your site because you are using the default domain (charkeyqk.github.io) , Unavailable for your site because you have a custom domain configured (blog.javarevisited.me)
	
	![Settings of Github Pages](/img/in-post/building-your-own-blog/blog-hello-world.jpg)

7. 再查看一下仓库 **Settings** > Github Pages 的配置情况
![Settings of Github Pages](/img/in-post/building-your-own-blog/settings-github-pages.jpg)

到这里，我们已经可以使用 Github Pages 发布我们自己的页面了。

### 如何创建 Project site

1. 创建一个新的仓库或者使用已有的仓库，在仓库中创建一个新的分支 `gh-pages`
![Create branch: gh-pages](/img/in-post/building-your-own-blog/create-branch-gh-pages.jpg)

2. 参考 [如何创建 User site](#user-site) 的操作，克隆仓库并创建 index.html，只不过这回需要提交到 `gh-pages` 这个分支。当然也可以将 `gh-pages` 设置为默认分支。
![Branches: default branch](/img/in-post/building-your-own-blog/branches-default-branch.jpg)

3. 在创建完 `gh-pages` 分支，**Settings** > Github Pages 会自动将此分支设置为站点的 Source。
	![Settings of Github Pages](/img/in-post/building-your-own-blog/github-pages-source.jpg)

4. 访问 Project site

	如上图所示: *Your site is published at https://charkeyqk.github.io/blog2.javarevisited.me/* 默认 Project Site 的域名是 *https://username.github.io/repository_name/*。


### 域名解析设置

*Custom domains allow you to serve your site from a domain other than charkeyqk.github.io*

1. 假设我们希望绑定 *blog2.javarevisited.me* 到 Github Pages，则需要在域名解析配置如下：
![Settings of Github Pages](/img/in-post/building-your-own-blog/dns-cname.jpg)
*记录值那边填写的是自己 Github Pages 的默认域名，比如我的是 charkeyqk.github.io*

2. Custom domain 设置
在 Settings > Github Pages 设置 **Custom domain** 为自定义域名，点击 Save 保存，即可看到 *Your site is ready to be published at http://blog2.javarevisited.me/*，等 Github Pages 获取到域名的 DNS 解析记录之后就会发布站点。
![Github Pages custom domain](/img/in-post/building-your-own-blog/github-pages-custome-domain.jpg)

	>如果域名解析配置存在问题，Github Pages 会出现这样的错误提示：*Domain's DNS record could not be retrieved. For more information, see https://help.github.com/articles/setting-up-a-custom-domain-with-github-pages/.*

3. 等待域名解析同步之后，就能使用自定义域名访问了。

Read more: [如何绑定域名到 Github Pages](https://help.github.com/articles/about-supported-custom-domains/)

---

## Jekyll 模版系统

关于 [Jekyll](http://jekyllrb.com/) 更多的请到官网去了解。

那是不是默认的 Jekyll 主题都有我这博客这么好看了？

当然不是。

不过我这博客主题也是“拿来主义”，使用的是 [Hux](http://huangxuan.me/) 的 Hux blog 模板。

这里不建议直接 clone 他的博客仓库，还是去 clone 他给大家做的一个稳定版的模版：
```shell
git clone git@github.com:Huxpro/huxblog-boilerplate.git
```

那么怎么修改模版呢？阅读 Hux blog 模版的说明文档 [README.zh.md](https://github.com/Huxpro/huxpro.github.io/blob/master/README.zh.md)

## Jekyll 本地环境搭建

Jekyll 是用 Ruby 语言写的一个静态网页生成工具，所以要搭建 Jekyll 本地环境就需要先配置好Ruby环境。下面分别描述一下如何在 Windows 和 Ubuntu 系统上配置 Ruby 环境。

### Windows 环境

1. 去官网下载 Ruby，[安装 Ruby](https://www.ruby-lang.org/zh_cn/documentation/installation/)，Windows 环境使用 [RubyInstaller](https://www.ruby-lang.org/zh_cn/documentation/installation/#rubyinstaller) 能帮助我们安装所需的所有 Ruby 开发环境。下载、运行，即可！
> 下载地址：[rubyinstaller-2.3.1 x86](http://dl.bintray.com/oneclick/rubyinstaller/rubyinstaller-2.3.1.exe) [rubyinstaller-2.3.1 x64](http://dl.bintray.com/oneclick/rubyinstaller/rubyinstaller-2.3.1-x64.exe)
>
> 安装时记得勾选 **Add Ruby executables to your PATH** 以便安装程序自动为我们配置系统环境变量。

2. 打开 CMD，输入如下命令安装 Jekyll，然后请耐心等待安装完成。
```shell
gem install jekyll
```

3. 安装验证
```shell
$ ruby -v
ruby 2.3.1p112 (2016-04-26 revision 54768) [x64-mingw32]
$ gem -v
2.5.1
$ jekyll -v
jekyll 3.2.1
```

### Ubuntu 环境

在 Ubuntu 上安装 Ruby 2.0.0 以上版本：

1. 在 [Ruby 官网](https://www.ruby-lang.org/en/downloads/) 上 **Stable releases** 部分找到最新稳定版的源码下载地址
> 如：https://cache.ruby-lang.org/pub/ruby/2.3/ruby-2.3.1.tar.gz

2. 按照如下的命令执行即可
```shell
sudo apt-get -y update
sudo apt-get -y install build-essential zlib1g-dev libssl-dev libreadline6-dev libyaml-dev
cd /tmp
wget https://cache.ruby-lang.org/pub/ruby/2.3/ruby-2.3.1.tar.gz
tar -xvzf ruby-2.3.1.tar.gz
cd ruby-2.3.1/
./configure --prefix=/usr/local
make
sudo make install
```

3. Jekyll 的安装同 Windows 环境

## Jekyll 本地运行调试

新建网站使用命令 *jekyll new 网站名*，Jekyll 会在当前目录下新建一个以网站名为名的文件夹。具体的目录结构及其用途，请到官网 [Jekyll](http://jekyllrb.com/) 学习。

前面我提到这个博客使用的是 Hux 的模版.

在 clone 模版仓库后，进入仓库目录，执行 **jekyll serve**，看到如下输出即可访问本地博客。
```
Server address: http://127.0.0.1:4000/
Server running... press ctrl-c to stop.
```

### 怎么写博客？

直接去模版仓库下 `_posts` copy 一份然后修改！

当然，更多的相关的语法，比如 Markdown、Jekyll、HTML、JavaScript，还有 Git 命令、Shell 命令，不懂的都需要自己再去学习学习。
