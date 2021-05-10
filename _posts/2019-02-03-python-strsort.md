---
layout:      post
title:       "python学习记录"
subtitle:    "----元组、列表、字符串排序"
date:        2019-02-03
author:      "levi"
header-img:  "img/post-bg-python-str.png"
tags:
  - python
---

>> 主要讲解一下的字符串、列表中的内置排序原理


列表中常用的几个函数
Python包含以下方法:

```
序号	方法
1. list.append(obj)
在列表末尾添加新的对象
2. list.count(obj)
统计某个元素在列表中出现的次数
3. list.extend(seq)
在列表末尾一次性追加另一个序列中的多个值（用新列表扩展原来的列表）
4. list.index(obj)
从列表中找出某个值第一个匹配项的索引位置
5. list.insert(index, obj)
将对象插入列表
6. list.pop([index=-1])
移除列表中的一个元素（默认最后一个元素），并且返回该元素的值
7. list.remove(obj)
移除列表中某个值的第一个匹配项
8. list.reverse()
反向列表中元素
9. list.sort(cmp=None, key=None, reverse=False)
对原列表进行排序
```

主要讲解一下双冒号的作用[::]
Python sequence slice addresses can be written as a[start:end:step] and any of start, stop or end can be dropped.

Python序列切片地址可以写为[开始：结束：步长]，其中的开始和结束可以省略
开始为大于等于，结束为小于
```
a =[0,1,2,3,4,5,6,7,8,9]
a[::-1]
[9,8,7,6,5,4,3,2,1,0]
a[2:4:1]
#以第二个元素开始到第四个元素截止、步长为1
[2,3]
```

开始start省略时，默认从第0项开始
```
a[:10:2]
[0,2,4,6,8]
```
结尾省略的话，默认到数组最后
```
a[1::2]
[1,3,5,7,9]
```
开始和结尾不省略的话，step默认为1
```
a[2:6:]
[2,3,4,5]
a[2:6:1]
[2,3,4,5]
```
当step为负数的时候，从右向左取数
```
a[::-2]
[9,7,5,3,1]
```



