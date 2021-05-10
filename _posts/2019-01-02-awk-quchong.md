---
layout:      post
title:       "awk脚本学习"
subtitle:    "文本去重"
date:        2019-01-02
author:      "levi"
header-img:  "img/post-bg-Shell-Script.jpg"
tags:
  - linux
---

> 由于经常要处理日志文件，而里面又有很多的重复文件，故而记录一下awk出重的方法。



#### awk根据某一列的关键词去重

现有文件a.txt

```shell
[root@localhost]# cat a.txt 
adc 3 5 
a d a
a 3 adf
a d b
a 3 adf
```

1. 方法一

去重第一列重复的行(第一列都包含a)：

```shell
[root@localhost]# cat a.txt |awk '!a[$1]++{print}'
adc 3 5 
a d a
```

重复的行取最上面一行记录

去重以第一列和第二列重复的行：

```shell
[root@localhost]# cat a.txt |awk '!a[$1" "$2]++{print}'
adc 3 5 
a d a
a 3 adf
```

去除完全重复的行：

```shell
[root@localhost]# cat a.txt |awk '!a[$0]++{print}'
adc 3 5 
a d a
a 3 adf
a d b
```

只显示重复行：

```shell
[root@localhost]# cat a.txt |awk 'a[$0]++{print}'
a 3 adf
```













