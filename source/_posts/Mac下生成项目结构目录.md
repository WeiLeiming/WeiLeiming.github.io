---
title: Mac下生成项目结构目录
date: 2017-09-18 10:09:39
categories: 奇淫巧技
tags:
	- Mac
	- 奇淫巧技
---

## Install [tree](http://braumeister.org/formula/tree)

```shell
$ brew install tree
```

## Help

```shell
$ man tree
```

## Usage

```shell
$ tree -L 4 > directory.txt
```

<!-- more -->

`-L 4`表示向下遍历目录最大级数。

然后将目录结构输出到`directory.txt`中。

更多用法查看help。

```
.
├── LICENSE
├── README.md
├── backup
├── conf
├── directory.txt
├── dist
├── ios
├── requirements.txt
└── www
    ├── __pycache__
    │   ├── apis.cpython-36.pyc
    │   ├── config.cpython-36.pyc
    │   ├── config_default.cpython-36.pyc
    │   ├── config_override.cpython-36.pyc
    │   ├── coroweb.cpython-36.pyc
    │   ├── coroweb_test.cpython-36.pyc
    │   ├── handlers.cpython-36.pyc
    │   ├── markdown2.cpython-36.pyc
    │   ├── models.cpython-36.pyc
    │   └── orm.cpython-36.pyc
    ├── apis.py
    ├── app.py
    ├── app_coroweb_test.py
    ├── config.py
    ├── config_default.py
    ├── config_override.py
    ├── coroweb.py
    ├── coroweb_test.py
    ├── handlers.py
    ├── markdown2.py
    ├── models.py
    ├── orm.py
    ├── orm_test.py
    ├── pymonitor.py
    ├── schema.sql
    ├── static
    │   ├── css
    │   │   ├── addons
    │   │   ├── awesome.css
    │   │   ├── uikit.almost-flat.min.css
    │   │   ├── uikit.gradient.min.css
    │   │   └── uikit.min.css
    │   ├── fonts
    │   │   ├── FontAwesome.otf
    │   │   ├── fontawesome-webfont.eot
    │   │   ├── fontawesome-webfont.ttf
    │   │   └── fontawesome-webfont.woff
    │   ├── img
    │   │   └── user.png
    │   └── js
    │       ├── awesome.js
    │       ├── jquery.min.js
    │       ├── sha1.min.js
    │       ├── sticky.min.js
    │       ├── uikit.min.js
    │       └── vue.min.js
    └── templates
        ├── __base__.html
        ├── blog.html
        ├── blogs.html
        ├── manage_blog_edit.html
        ├── manage_blogs.html
        ├── manage_comments.html
        ├── manage_users.html
        ├── register.html
        ├── signin.html
        └── test.html

13 directories, 54 files
```

