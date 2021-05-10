---
layout:      post
title:       "windows系统修改启动引导项"
subtitle:    "BCD 启动和故障恢复"
date:        2018-10-15
author:      "levi"
header-img:  "img/post-bg-windows-cortana.jpg"
music: true
music-id:    1334647784
tags:
  - windows
---


> 不重新安装操作系统的前提下，删除/修改不需要的启动项。


1、在“启动和故障恢复”中可以看到当前系统的启动项，如下图，可修改/删除启动引导顺序。
![post-bg-system.jpg](https://i.loli.net/2021/05/09/w1Ypz6HhAnyVtOK.jpg)

2、使用"bootcfg"DOS命令显示开机启动项：

+ win+R打开cmd，需要以管理员方式打开
+ 输入"bcdedit /enum"，列出开机启动项信息，复制需要删除的启动项的"标识符"即"ID"
  ![bcdedit_enum.jpg](https://i.loli.net/2021/05/09/uO5IzNq8wPbR4fy.jpg)
+ 在命令行下输入"bcdedit /delete 标签符"(注意这个标签是包含{}的)命令并按回车键来删除开机启动菜单项。其中“标识符”是通过右击“粘贴”过来的
+ 显示"操作成功完成"时删除成功，通过"bcdedit /enum"命令显示已无不需要的开机启动项了





