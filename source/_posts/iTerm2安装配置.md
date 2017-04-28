---
title: iTerm2安装配置
date: 2017-03-21 17:07:32
tags:
	- 奇淫巧技
typora-copy-images-to: ipic
---

偶然看见一个比较喜欢的终端界面

![222](https://ww3.sinaimg.cn/large/006tKfTcly1fdukrbiu9lj30hl09m768.jpg)

于是尝试配置了出来，写这篇博客记录下步骤。以下适用于Mac。

<!-- more -->

# 下载安装[iTerm2](http://www.iterm2.com/)

Mac自带了Zsh。想要最新版可以通过下面的命令安装

```
$ brew install zsh
```

查看Zsh版本

```
$ zsh --version
```

iTerm2默认使用dash，改成Zsh

```
$ chsh -s /bin/zsh
```

# 安装[oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)

打开iTerm2，通过`curl` 或 `wget`安装

**via curl**

```
$ sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

**via wget**

```
$ sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
```

设置主题

```
$ vim ~/.zshrc
```

修改

```
ZSH_THEME="agnoster"
```

# 安装[solarized](https://github.com/altercation/solarized)

下载工程，进入刚刚下载的工程`solarized/iterm2-colors-solarized` 下双击 `Solarized Dark.itermcolors` 把配置文件导入到 iTerm2 里中

将iTerm2配色方案配置为Solarized Dark

![222](https://ww2.sinaimg.cn/large/006tKfTcly1fdvgkooci7j30pe0h0q5g.jpg)

# 安装[powerline](http://powerline.readthedocs.io/en/latest/installation.html)

安装powerline

```
$ pip install powerline-status
```

如果没有pip指令，先安装pip

```
$ sudo easy_install pip
```

如执行指令`pip install powerline-status`出错

一个可能是没下载安装苹果官方的[Command line](https://developer.apple.com/download/more/?name=for%20Xcode%20)，如果已经有了依然出错可能是权限问题，使用如下命令安装

```
$ pip install powerline-status --user
```

# 安装[字体库](https://github.com/powerline/fonts)

下载工程，进入工程目录，执行

```
$ ./install.sh
```

如提示

```
All Powerline fonts installed to /Users/superdanny/Library/Fonts
```

则复制成功。

将iTerm2字体修改为Meslo LG S Regular for Powerline，12pt

![222](https://ww1.sinaimg.cn/large/006tKfTcly1fdvh22nwk2j30pa0f4mzc.jpg)

# 安装[zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting)

安装

```
$ git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

在`~/.zshrc`中添加

```
plugins=(zsh-syntax-highlighting)
```

然后执行

```
$ source ~/.zshrc
```