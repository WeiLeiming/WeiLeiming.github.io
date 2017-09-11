---
title: 记录Mac安装MySQL Community Server
date: 2017-08-09 15:23:22
tags:
	- Mac
	- MySQL
---

最后更新时间：2017.9.11

# 安装

1. 下载 [MySQL Community Server](https://dev.mysql.com/downloads/mysql/) DMG安装包
2. 点击安装包安装。**注意：**在最后安装完成后会弹出一个标题为**MySQL Installer**的提示框，里面有这么句话 `root@localhost: XXXXXXXXXXXX` 冒号后面是密码，复制记住。
3. 安装成功

<!-- more -->

# 配置

1. 打开系统偏好设置

2. 点击 `MySQL`

3. 点击 `Start MySQL Server` 开启MySQL服务

4. 打开命令行，这个时候命令行输入 `mysql` 会提示找不到命令。配置系统环境变量，命令行输入命令：

   使用bash，编辑`~/.bash_profile`

   ```
   $ vim ~/.bash_profile
   ```

   使用zsh，则编辑`~/.zshrc`

   ```
   $ vim ~/.zshrc
   ```

5. 使用vim编辑，添加：

   ~/.bash_profile

   ```
   export PATH="$PATH:/usr/local/mysql/bin"
   ```

   ~/.zshrc

   ```
   export PATH=$HOME/mysql/bin:/usr/local/mysql/bin:$PATH
   ```

6. 输入命令，立即生效

   bash

   ```
   $ source ~/.bash_profile
   ```

   zsh

   ```
   $ source ~/.zshrc
   ```

7. 输入命令，登录MySQL

   ```
   $ mysql -u root -p
   ```

8. 提示输入密码，输入安装包刚安装完后弹出的提示框中的初始密码

9. 成功进入MySQL后，修改初始密码，输入命令，newpass替换为自己的密码

   ```
   $ SET PASSWORD FOR 'root'@'localhost' = PASSWORD('newpass');
   ```

10. 修改完成