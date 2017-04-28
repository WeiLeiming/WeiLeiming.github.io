---
title: 使用Hexo搭建个人博客
date: 2017-03-09 11:05:28
tags:
	- 奇淫巧技
	- Hexo
typora-copy-images-to: ipic
---

总结下自己用[Hexo](https://github.com/hexojs/hexo)搭建博客的过程。整个博客是挂在GitHub Pages上的，域名是自己买的，搭建时Hexo是3.2.2版本，因为自己用的Mac电脑，所以此篇适用于苹果电脑。废话不多说，正式开始吧。

<!-- more -->

# 环境配置

## 安装Node.js

到Node.js[官网](https://nodejs.org/en/)下载对应平台的最新版本，按提示安装即可

## 安装Git

因为我已经装了Xcode所以自带Git（否则会叫安装Xcode，那就安装咯）

## Hexo安装

Node.js和Git安装好后就可以安装Hexo了

```
$ npm install hexo-cli -g
$ hexo init blog
$ cd blog
$ npm install
$ hexo server
```

如果没出错，就可以在本地服务上`http://localhost:4000/`查看效果了。

这是我blog文件夹下的目录结构

![3B045A84-0EBE-42ED-A785-452CB5C2E187](https://ww1.sinaimg.cn/large/006tNc79ly1fdgfvbdgrfj3058042gln.jpg)

配置修改在**_config.yml**中，新建的md文件都放置在`/source/_posts`中，**themes**放置的是主题

# 介绍下Hexo常用的命令

新建文章

```
$ hexo new "文章名称"
```

生成静态网页

```
$ hexo g
```

启动本地服务

```
$ hexo s
```

上传部署

```
$ hexo d
```

# GitHub配置

## 申请账号

略

## 新建Repository

这里只有一个地方需要说明，就是Repository name。

填写格式为：username.github.io

比如我的账号是weileiming，那么我的主页地址就是weileiming.github.io

![222](https://ww1.sinaimg.cn/large/006tNbRwly1fdhnxm4h4bj30n40h00uo.jpg)

我这因为已经建过了所以报了这个错，没有新建过的话再点Create repository就可以建立了

还不懂的可以看看[官方文档](https://help.github.com/articles/user-organization-and-project-pages/)

## Hexo关联Repository

在_config.yml中将新建的仓库添加上

![222](https://ww2.sinaimg.cn/large/006tNc79ly1fdhpymx9gcj30ei02taa6.jpg)

## Github Pages个人主页绑定个人域名

如果有个人域名的话，可以绑定自己的个人域名。[官方文档](https://help.github.com/articles/quick-start-setting-up-a-custom-domain/)

如何申请个人域名百度去吧。

先把IP地址ping出来

![ping](http://ww3.sinaimg.cn/large/006tKfTcly1fem91j1nokj30cb02nwf1.jpg)

之后添加解析

![解析](http://ww2.sinaimg.cn/large/006tKfTcly1fem93jgmnzj30n3021mx6.jpg)

去刚刚建立的仓库里面点击Create new file

文件名填写CNAME，内容填写个人域名，如willwei.me，commit上去就可以用个人域名访问了

当然如果是拿来用hexo做博客的话这样CNAME是会被干掉的，所以这样搞不行，我将CNAME放在了这里

![333](https://ww4.sinaimg.cn/large/006tNc79ly1fdgliu4jy8j30b1047wen.jpg)

然后在用hexo一起部署上去

# Hexo的一些主题

以下是我个人喜欢的一些主题，集成步骤都比较简单

[hexo-theme-next](https://github.com/iissnan/hexo-theme-next)

[hexo-theme-yilia](https://github.com/litten/hexo-theme-yilia)

我使用的是yilia主题，修改配置的话，主题的配置文件是themes/yilia下**_config.yml**

## 一些技巧(yilia为例)

在文章中加`<!--more-->`可以使这之后的内容隐藏

如果要修改截断文字，可以在`themes/yilia/_config.yml`中配置

![截断文字](http://ww1.sinaimg.cn/large/006tKfTcly1fem952769nj305400vmx2.jpg)

# 更多配置

## RSS订阅及SEO

在博客根目录下安装`hexo-generator-feed`、`hexo-generator-baidu-sitemap`、`hexo-generator-sitemap`

```
$ npm install hexo-generator-feed --save
$ npm install hexo-generator-baidu-sitemap --save
$ npm install hexo-generator-sitemap --save
```

在博客根目录下的`_config.yml`中添加

```
sitemap:
    path: sitemap.xml
    
baidusitemap:
    path: baidusitemap.xml

feed:
    type: atom
    path: atom.xml
    limit: 100
```

## Hexo升级

在博客根目录下

```
$ npm upgrade hexo
```

## yilia升级

```
$ cd themes/yilia
$ git pull
```