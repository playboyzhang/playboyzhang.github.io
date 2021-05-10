---
layout:      post
title:       "git学习笔记"
subtitle:    "----git冲突解决"
date:        2019-01-25
author:      "levi"
header-img:  "img/post-bg-github-cup.jpg"
tags:
  - 学习笔记
  - git
---

> git上冲突解决方法

在使用git的时候，我们经常是家里和工作地点同时使用，这样有时就会出现代码冲突。

我在家里有些修改没有提交，再次从远程仓库pull的时候就会报以下错误：

```shell
error:  ×××××××××××××××××××××××××××××××××××××
Please, commit your changes or stash them before you can merge.
```

这时候有两种处理方法：

- 保留本地修改，仅仅并入新的配置项
  ```shell
  git stash
  git pull
  git stash pop
  ```

- 代码库中的文件完全覆盖本地

  ```
  git reset --hard
  git pull
  ```
  其中git reset是针对版本,如果想针对文件回退本地修改,使用`git checkout HEAD file/to/restore`


  git还原参数说明：
  还原之后会删除本地的commit，如果本地commit且没有push到远程的记录就会丢失

  git reset –mixed：此为默认方式，不带任何参数的git reset，即时这种方式，它回退到某个版本，只保留源码，回退commit和index信息　　

  

  git reset –soft：回退到某个版本，只回退了commit的信息，不会恢复到index file一级。如果还要提交，直接commit即可　　

  以上两个都不会修改文件内容，区别就是恢复到add之前还是之后

  

  git reset –hard：彻底回退到某个版本，本地的源码也会变为上一个版本的内容



