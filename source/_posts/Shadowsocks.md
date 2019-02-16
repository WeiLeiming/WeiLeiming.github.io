---
title: Shadowsocks
date: 2018-11-30 20:34:00
categories: 奇淫巧技
tags:
	- 奇淫巧技
	- Mac
---

最后更新时间：2019.2.16

## Shadowsocks部署

### 第一步

自备国外的VPS，我使用的是[Vultr](https://www.vultr.com/)家的日本线路

### 第二步

使用[teddysun](https://github.com/teddysun)的一键脚本

```shell
$ wget --no-check-certificate https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-all.sh && chmod +x shadowsocks.sh && ./shadowsocks-all.sh
```

选择想要安装的版本即可。

<!-- more -->

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

