---
title: Mac命令行代理配置
date: 2017-09-11 11:34:34
categories: 奇淫巧技
tags:
	- Mac
	- 奇淫巧技
typora-copy-images-to: ipic
---

本教程建立在已安装并配置好[Shadowsocks](https://github.com/shadowsocks/ShadowsocksX-NG)基础上，适用于Mac电脑。

![C330FAF9-3F03-43EB-A13B-278304156749](https://ws3.sinaimg.cn/large/006tKfTcly1fjfmoyf8ysj304604ejrp.jpg)

<!-- more -->

# 使用polipo

1. 安装[polipo](https://github.com/jech/polipo)

   ```
   $ brew install polipo
   ```

2. 打开polipo配置文件`/usr/local/opt/polipo/homebrew.mxcl.polipo.plist`，加上`<string>socksParentProxy=localhost:1086</string>`

   Socks5的监听端口号从Shadowsocks偏好设置中获取：

   ![E3A8355D-C3E1-41B7-AB24-CA9D52B6089D](https://ws1.sinaimg.cn/large/006tKfTcly1fjfm06l1isj30fo0c075g.jpg)

   ![0222370E-6B7E-48EF-9E20-4D2F34D99BC9](https://ws3.sinaimg.cn/large/006tKfTcly1fjflyxv4k5j30k30auq4q.jpg)

3. 启动polipo

   ```
   $ ln -sfv /usr/local/opt/polipo/*.plist ~/Library/LaunchAgents 
   $ launchctl load ~/Library/LaunchAgents/homebrew.mxcl.polipo.plist
   ```

4. 设置代理

   在命令行配置文件(bash是~/.bash_profile，zsh是~/.zshrc)中添加：

   ```
   alias poff="unset http_proxy; unset https_proxy"
   alias pon="export http_proxy=http://localhost:8123;export https_proxy=http://localhost:8123"
   ```

5. 立即生效配置文件

   ```
   bash:
   $ source ~/.bash_profile
   zsh:
   $ source ~/.zshrc
   ```

6. 使用方法，命令行执行

   开启：pon

   关闭：poff

7. 测试使用

   ```
   $ pon
   $ curl ip.cn
   ```

   以下是我的代理

   ![A847B781-288E-4D9E-AF81-D53C51A6821A](https://ws3.sinaimg.cn/large/006tKfTcly1fjfmjl2s6ij30bi00xwej.jpg)

# 新方法

polipo目前已经停止维护，但是现在Mac版的Shadowsocks已经支持HTTP代理，所以命令行翻墙已经不需要这么复杂了。

1. 复制Shadowsocks代理命令

   ![6E64ED2D-EBDA-4F08-9AF4-40142568AC1C](https://ws2.sinaimg.cn/large/006tKfTcly1fjfmsowdlmj307m0a6go3.jpg)

2. 命令行执行复制的命令，如：

   ```
   $ export http_proxy=http://127.0.0.1:1087;export https_proxy=http://127.0.0.1:1087;
   ```

   测试下

   ```
   $ curl ip.cn
   ```

   到这一步已经可以了，但是这样每次开个终端都得重新输入命令，所以将命令直接写入配置文件。

3. 配置文件修改

   在命令行配置文件(bash是~/.bash_profile，zsh是~/.zshrc)中添加：

   ```
   alias poff="unset http_proxy; unset https_proxy"
   alias pon="export http_proxy=http://127.0.0.1:1087;export https_proxy=http://127.0.0.1:1087"
   ```

4. 立即生效配置文件

   ```
   bash:
   $ source ~/.bash_profile
   zsh:
   $ source ~/.zshrc
   ```

5. 使用方法，命令行执行：

   开启：pon

   关闭：poff

