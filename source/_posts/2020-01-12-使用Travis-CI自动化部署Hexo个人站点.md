---
title: 使用Travis CI自动化部署Hexo个人站点
tags:
  - Travis CI
  - Hexo
mathjax: true
abbrlink: 1913110b
date: 2020-01-12 14:50:47
---

在刚开始使用Hexo+Github Pages搭建个人博客时，都是先在本地写好文章，再通过终端输入如下命令：

```bash
hexo clean
hexo g
hexo d
```

将新文章部署到Github Pages上去。初始时并未有什么不方便。

<!-- more -->

但是，随着使用的次数增加，发现在写文章的机器上必须需要安装hexo和Node.js环境，当更换一台电脑（比如出差或者旧电脑损坏）之后，想要写博客，就需要重新安装并配置博客环境（尤其当在之前的机器上对配置做了一些内部的修改之后），经常就会发现，配置起来很繁琐，而且某些当时的配置（比如我个人对在网页上进行`latex`支持的配置，可见我的博文[在Hexo的Next主题中支持Latex](https://aipikachu.me/posts/2631/)）很难忆起做过什么修改的时候。
