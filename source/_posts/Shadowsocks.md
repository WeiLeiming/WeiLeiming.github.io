---
title: Shadowsocks
date: 2018-11-30 20:34:00
categories: 奇淫巧技
tags:
	- 奇淫巧技
	- Mac
---

## BBR加速

安装

```shell
$ wget –no-check-certificate https://github.com/teddysun/across/raw/master/bbr.sh && chmod +x bbr.sh && ./bbr.sh
```

返回值一般为net.ipv4.tcp_available_congestion_control = reno cubic bbr说明BBR已完成安装

```shell
$ sysctl net.ipv4.tcp_available_congestion_control
```

返回值有tcp_bbr模块说明BBR已启动

```shell
$ lsmod | grep bbr
```

