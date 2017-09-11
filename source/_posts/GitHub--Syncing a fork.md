---
title: GitHub--Syncing a fork
date: 2017-09-11 10:35:00
tags:
	- GitHub
	- 奇淫巧技
---

1. `git remote -v` — 查看所有远程主机及地址

   ```
   $ git remote -v
   origin  https://github.com/YOUR_USERNAME/YOUR_FORK.git (fetch)
   origin  https://github.com/YOUR_USERNAME/YOUR_FORK.git (push)
   ```

2. `git remote add upstream` — 添加远程主机

   ```
   $ git remote add upstream https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY.git
   ```

   查看添加后远程主机 — `git remote -v`

   ```
   $ git remote -v
   origin    https://github.com/YOUR_USERNAME/YOUR_FORK.git (fetch)
   origin    https://github.com/YOUR_USERNAME/YOUR_FORK.git (push)
   upstream  https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY.git (fetch)
   upstream  https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY.git (push)
   ```

   <!-- more -->

3. `git fetch upstream` — 从远程获取最新版本

4. `git checkout master` — 确保在`master`分支

5. `git merge upstream/master` — 将`upstream/master`上的更改合并到本地`master`分支