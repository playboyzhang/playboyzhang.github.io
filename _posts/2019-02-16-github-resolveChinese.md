---
layout:      post
title:       "git学习笔记"
subtitle:    "----git中文文件"
date:        2019-02-16
author:      "Boy's Zhang"
header-img:  "img/post-bg-github-cup.jpg"
tags:
  - 学习笔记
  - git
---

> git上解决中文文件显示、修改、提交问题

一般git本地仓库命名的中文文件会在本地以这种形式存在
![in-pot-github-contact](/img/in-post/2019-02-16-github-resolveChinese/WrongChinese.png)

故而在修改或者删除时不管加不加git提示的那个双引号，用git删除时都会提示路径找不到

解决方法有两种
- 修改git配置

 ``` git
 git config --global core.quotepath false
 ```

git config --help中显示
```git
       core.quotePath
           The commands that output paths (e.g.  ls-files, diff), when not given the -z option, will quote "unusual" characters in the pathname by enclosing the
           pathname in a double-quote pair and with backslashes the same way strings in C source code are quoted. If this variable is set to false, the bytes
           higher than 0x80 are not quoted but output as verbatim. Note that double quote, backslash and control characters are always quoted without -z regardless
           of the setting of this variable.
```
所以core.quotepath改为false的话，git不会对值为128以上的非ascii码字符进行quote，就会显示中文

- 修改～/.gitconfig

添加
```git
[core]
quotepath = false
```
原理同上