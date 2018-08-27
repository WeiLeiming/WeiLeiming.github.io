---
title: 配置SSH key
date: 2018-08-27 21:29:16
categories: 奇淫巧技
tags:
	- Mac
	- 奇淫巧技
---

最后更新时间：2018.8.27

这里以 GitHub 举例

生成新的 SSH key

```shell
$ ssh-keygen -t rsa -C "xxx@mail.com" -f ~/.ssh/github_id_rsa
```

配置 config 文件

```shell
$ vi ~/.ssh/config
```

<!-- more -->

如没有，则新建再配置

```shell
$ touch ~/.ssh/config
```

在 config 文件中新增

```
## github
Host github.com
    HostName github.com
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/github_id_rsa
```

接着在 GitHub 网站中加入公钥

验证是否成功

```shell
$ ssh -T git@github.com
```

如果出现

```
Hi username! You've successfully authenticated, but GitHub does not provide shell access.
```

则表示成功，其它也类似操作。