---
layout:      post
title:       "python学习记录"
subtitle:    "io模块分割文件"
date:        2018-10-08
author:      "Boy's Zhang"
header-img:  "img/post-bg-python.jpg"
tags:
  - python
  - os
---


> io 模块提供了根据文件的路径来分割文件目录、文件名、文件后缀。

代码示例
=============================

```python
import os
os.path.realpath(_file_) #返回当前文件的路径
os.path.split() #返回文件的路径和文件名
os.getcwd()   #获取当前工作目录
print(os.path.realpath(__file__))
print(os.getcwd()) 
file_path = "D:/test/test.py"

(file_path,tempfilename) = os.path.split(file_path)
(filename,extension) = os.path.splitext(tempfilename)

print(file_path,tempfilename)
print(filename,extension)
```

结果
================================
```python
D:/test/test.py
D:/test
D:/test test.py
test .py
```








