---
title: 解决Mac系统无法设置以太网IP地址
date: 2017-05-27 14:40:08
tags:
	- Mac
	- 奇淫巧技
typora-copy-images-to: ipic
---

在macOS Sierra 10.12.5下手动设置IP地址后弹出错误提示

![9D3A22E8-DE89-4743-ADC4-CB2755B1F3E2](https://ws4.sinaimg.cn/large/006tNc79ly1ffzx0duzqzj30bo03nt8x.jpg)

可使用命令行直接设置以太网的IP地址，对应IP地址、子网掩码、路由器。

```
$ networksetup -setmanual Ethernet 192.168.1.165 255.255.255.0 192.168.1.1
```