---
layout:      post
title:       "python学习记录"
subtitle:    "enumerate函数"
date:        2018-10-04
author:      "BOY"
header-img:  "img/post-bg-python.jpg"
tags:
  - python
  - enumerate()
  - 文件行数
---


> 在写列表循环时，需要根据索引来取键值，以前没有用过类似的函数，故记录下



- enumerate() 函数用于将一个可遍历的数据对象(如列表、元组或字符串)组合为一个索引序列，同时列出数据和数据下标
- 函数为python内置函数，Python 2.3. 以上版本可用
- 一般用在 for 循环当中

### 语法 

```
enumerate(sequence, [start=0])
```

- sequence -- 一个序列、迭代器或其他支持迭代对象。
- start -- 下标起始位置。

### 返回值

返回 enumerate(枚举) 对象。


### 实例

在一个列表中，如果既要遍历索引，又要遍历元素时：
- 不用enumerate



      seasons = ['Spring', 'Summer', 'Fall', 'Winter']  
      for i in range(len(seasons))  
          print (i,seasons[i])  


结果

```

0 Spring
1 Summer
2 Fall
3 Winter
```

- 使用enumerate

```

    seasons = ['Spring', 'Summer', 'Fall', 'Winter']
    for index, item in enumerate(seasons):
        print (index, item)
```

结果

```

0 Spring
1 Summer
2 Fall
3 Winter
```
这样代码看起来就简洁很多

- enumerate()，也可传参，默认不传参数时从第一个元素开始打印，也可传入下标起始位置

```

>>>seasons = ['Spring', 'Summer', 'Fall', 'Winter']
>>> list(enumerate(seasons))
[(0, 'Spring'), (1, 'Summer'), (2, 'Fall'), (3, 'Winter')]
>>> list(enumerate(seasons, start=1))       # 下标从 1 开始
[(1, 'Spring'), (2, 'Summer'), (3, 'Fall'), (4, 'Winter')]
```

- enumerate函数也可用于统计文件行数

```
flag = 0
for index, line in enumerate(open(filepath,'r'))： 
    flag += 1
print(flag)
```

- 简单粗暴的方法

```
count = len(open(filepath, 'r').readlines())
```


