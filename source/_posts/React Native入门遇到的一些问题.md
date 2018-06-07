---
title: React Native入门遇到的一些问题
date: 2017-09-11 17:08:10
tags:
	- React Native
---

最后更新时间：2018.6.7

记录学习[React Native](https://github.com/facebook/react-native)过程中踩过的坑。

<!-- more -->

## 镜像源设置

**npm**

```shell
$ npm config set registry https://registry.npm.taobao.org --global
$ npm config set disturl https://npm.taobao.org/dist --global
```

**yarn**

```shell
$ yarn config set registry https://registry.npm.taobao.org --global
$ yarn config set disturl https://npm.taobao.org/dist --global
```

## 安装React Native时出错

```shell
$ npm install -g react-native-cli
```

```shell
npm WARN checkPermissions Missing write access to /usr/local/lib/node_modules/react-native-cli
npm WARN checkPermissions Missing write access to /usr/local/lib/node_modules
/usr/local/lib
└── react-native-cli@2.0.1

npm ERR! Darwin 16.6.0
npm ERR! argv "/usr/local/bin/node" "/usr/local/bin/npm" "install" "-g" "react-native-cli"
npm ERR! node v6.11.3
npm ERR! npm  v3.10.10
npm ERR! path /usr/local/lib/node_modules/react-native-cli
npm ERR! code EACCES
npm ERR! errno -13
npm ERR! syscall access

npm ERR! Error: EACCES: permission denied, access '/usr/local/lib/node_modules/react-native-cli'
npm ERR!     at Error (native)
npm ERR!  { Error: EACCES: permission denied, access '/usr/local/lib/node_modules/react-native-cli'
npm ERR!     at Error (native)
npm ERR!   errno: -13,
npm ERR!   code: 'EACCES',
npm ERR!   syscall: 'access',
npm ERR!   path: '/usr/local/lib/node_modules/react-native-cli' }
npm ERR!
npm ERR! Please try running this command again as root/Administrator.

npm ERR! Please include the following file with any support request:
npm ERR!     /Users/willwei/ReactNative/npm-debug.log
```

权限问题

解决方法：

```shell
$ sudo npm install -g react-native-cli
```

## 初始化工程时出错

```shell
$ react-native init NewProject
```

```shell
This will walk you through creating a new React Native project in /Users/willwei/ReactNative/AwesomeProject
Using yarn v1.0.1
Installing react-native...
yarn add v1.0.1
info No lockfile found.
[1/4] 🔍  Resolving packages...
warning connect@2.30.2: connect 2.x series is deprecated
[2/4] 🚚  Fetching packages...
error An unexpected error occurred: "http://registry.npm.taobao.org/fbjs/download/fbjs-0.8.15.tgz: ETIMEDOUT".
info If you think this is a bug, please open a bug report with the information provided in "/Users/willwei/ReactNative/AwesomeProject/yarn-error.log".
info Visit https://yarnpkg.com/en/docs/cli/add for documentation about this command.
{ Error: Command failed: yarn add react-native --exact
    at checkExecSyncError (child_process.js:472:13)
    at execSync (child_process.js:512:13)
    at run (/usr/local/lib/node_modules/react-native-cli/index.js:294:5)
    at createProject (/usr/local/lib/node_modules/react-native-cli/index.js:249:3)
    at init (/usr/local/lib/node_modules/react-native-cli/index.js:200:5)
    at Object.<anonymous> (/usr/local/lib/node_modules/react-native-cli/index.js:153:7)
    at Module._compile (module.js:570:32)
    at Object.Module._extensions..js (module.js:579:10)
    at Module.load (module.js:487:32)
    at tryModuleLoad (module.js:446:12)
  error: null,
  cmd: 'yarn add react-native --exact',
  file: '/bin/sh',
  args: [ '/bin/sh', '-c', 'yarn add react-native --exact' ],
  options:
   { stdio: [ [Object], [Object], [Object] ],
     shell: true,
     file: '/bin/sh',
     args: [ '/bin/sh', '-c', 'yarn add react-native --exact' ],
     envPairs:
      [ 'TERM_SESSION_ID=w0t0p0:2155C89E-E974-4A48-8B9B-DA10C1F5B604',
        'SSH_AUTH_SOCK=/private/tmp/com.apple.launchd.mSKRWxvqHf/Listeners',
        'Apple_PubSub_Socket_Render=/private/tmp/com.apple.launchd.etmtp2jjxL/Render',
        'COLORFGBG=12;8',
        'ITERM_PROFILE=Default',
        'XPC_FLAGS=0x0',
        'LANG=zh_CN.UTF-8',
        'PWD=/Users/willwei/ReactNative',
        'SHELL=/bin/zsh',
        'TERM_PROGRAM_VERSION=3.0.14',
        'TERM_PROGRAM=iTerm.app',
        'PATH=/Users/willwei/.rvm/gems/ruby-2.3.0/bin:/Users/willwei/.rvm/gems/ruby-2.3.0@global/bin:/Users/willwei/.rvm/rubies/ruby-2.3.0/bin:/Users/willwei/mysql/bin:/usr/local/mysql/bin:/Library/Frameworks/Python.framework/Versions/3.6/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/Users/willwei/.rvm/bin',
        'TERM=xterm-256color',
        'HOME=/Users/willwei',
        'TMPDIR=/var/folders/00/l74f8gln3qxcpx9gzdxps6dc0000gn/T/',
        'USER=willwei',
        'XPC_SERVICE_NAME=0',
        'LOGNAME=willwei',
        'ITERM_SESSION_ID=w0t0p0:2155C89E-E974-4A48-8B9B-DA10C1F5B604',
        '__CF_USER_TEXT_ENCODING=0x1F5:0x19:0x34',
        'SHLVL=1',
        'OLDPWD=/Users/willwei',
        'ZSH=/Users/willwei/.oh-my-zsh',
        'PAGER=less',
        'LESS=-R',
        'LSCOLORS=Gxfxcxdxbxegedabagacad',
        'LC_CTYPE=zh_CN.UTF-8',
        'rvm_prefix=/Users/willwei',
        'rvm_path=/Users/willwei/.rvm',
        'rvm_bin_path=/Users/willwei/.rvm/bin',
        '_system_type=Darwin',
        '_system_name=OSX',
        '_system_version=10.12',
        '_system_arch=x86_64',
        'rvm_version=1.27.0 (latest)',
        'GEM_HOME=/Users/willwei/.rvm/gems/ruby-2.3.0',
        'GEM_PATH=/Users/willwei/.rvm/gems/ruby-2.3.0:/Users/willwei/.rvm/gems/ruby-2.3.0@global',
        'MY_RUBY_HOME=/Users/willwei/.rvm/rubies/ruby-2.3.0',
        'IRBRC=/Users/willwei/.rvm/rubies/ruby-2.3.0/.irbrc',
        'RUBY_VERSION=ruby-2.3.0',
        'rvm_alias_expanded=',
        'rvm_bin_flag=',
        'rvm_docs_type=',
        'rvm_gemstone_package_file=',
        'rvm_gemstone_url=',
        'rvm_niceness=',
        'rvm_nightly_flag=',
        'rvm_only_path_flag=',
        'rvm_proxy=',
        'rvm_quiet_flag=',
        'rvm_ruby_bits=',
        'rvm_ruby_file=',
        'rvm_ruby_make=',
        'rvm_ruby_make_install=',
        'rvm_ruby_mode=',
        'rvm_script_name=',
        'rvm_sdk=',
        'rvm_silent_flag=',
        'rvm_use_flag=',
        'rvm_wrapper_name=',
        'rvm_hook=',
        '_=/usr/local/bin/react-native' ] },
  envPairs:
   [ 'TERM_SESSION_ID=w0t0p0:2155C89E-E974-4A48-8B9B-DA10C1F5B604',
     'SSH_AUTH_SOCK=/private/tmp/com.apple.launchd.mSKRWxvqHf/Listeners',
     'Apple_PubSub_Socket_Render=/private/tmp/com.apple.launchd.etmtp2jjxL/Render',
     'COLORFGBG=12;8',
     'ITERM_PROFILE=Default',
     'XPC_FLAGS=0x0',
     'LANG=zh_CN.UTF-8',
     'PWD=/Users/willwei/ReactNative',
     'SHELL=/bin/zsh',
     'TERM_PROGRAM_VERSION=3.0.14',
     'TERM_PROGRAM=iTerm.app',
     'PATH=/Users/willwei/.rvm/gems/ruby-2.3.0/bin:/Users/willwei/.rvm/gems/ruby-2.3.0@global/bin:/Users/willwei/.rvm/rubies/ruby-2.3.0/bin:/Users/willwei/mysql/bin:/usr/local/mysql/bin:/Library/Frameworks/Python.framework/Versions/3.6/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/Users/willwei/.rvm/bin',
     'TERM=xterm-256color',
     'HOME=/Users/willwei',
     'TMPDIR=/var/folders/00/l74f8gln3qxcpx9gzdxps6dc0000gn/T/',
     'USER=willwei',
     'XPC_SERVICE_NAME=0',
     'LOGNAME=willwei',
     'ITERM_SESSION_ID=w0t0p0:2155C89E-E974-4A48-8B9B-DA10C1F5B604',
     '__CF_USER_TEXT_ENCODING=0x1F5:0x19:0x34',
     'SHLVL=1',
     'OLDPWD=/Users/willwei',
     'ZSH=/Users/willwei/.oh-my-zsh',
     'PAGER=less',
     'LESS=-R',
     'LSCOLORS=Gxfxcxdxbxegedabagacad',
     'LC_CTYPE=zh_CN.UTF-8',
     'rvm_prefix=/Users/willwei',
     'rvm_path=/Users/willwei/.rvm',
     'rvm_bin_path=/Users/willwei/.rvm/bin',
     '_system_type=Darwin',
     '_system_name=OSX',
     '_system_version=10.12',
     '_system_arch=x86_64',
     'rvm_version=1.27.0 (latest)',
     'GEM_HOME=/Users/willwei/.rvm/gems/ruby-2.3.0',
     'GEM_PATH=/Users/willwei/.rvm/gems/ruby-2.3.0:/Users/willwei/.rvm/gems/ruby-2.3.0@global',
     'MY_RUBY_HOME=/Users/willwei/.rvm/rubies/ruby-2.3.0',
     'IRBRC=/Users/willwei/.rvm/rubies/ruby-2.3.0/.irbrc',
     'RUBY_VERSION=ruby-2.3.0',
     'rvm_alias_expanded=',
     'rvm_bin_flag=',
     'rvm_docs_type=',
     'rvm_gemstone_package_file=',
     'rvm_gemstone_url=',
     'rvm_niceness=',
     'rvm_nightly_flag=',
     'rvm_only_path_flag=',
     'rvm_proxy=',
     'rvm_quiet_flag=',
     'rvm_ruby_bits=',
     'rvm_ruby_file=',
     'rvm_ruby_make=',
     'rvm_ruby_make_install=',
     'rvm_ruby_mode=',
     'rvm_script_name=',
     'rvm_sdk=',
     'rvm_silent_flag=',
     'rvm_use_flag=',
     'rvm_wrapper_name=',
     'rvm_hook=',
     '_=/usr/local/bin/react-native' ],
  stderr: null,
  stdout: null,
  pid: 7971,
  output: [ null, null, null ],
  signal: null,
  status: 1 }
Command `yarn add react-native --exact` failed.
```

解决方法：

重新设置镜像源

```shell
$ yarn config set registry https://registry.npm.taobao.org --global
$ yarn config set disturl https://npm.taobao.org/dist --global
```

