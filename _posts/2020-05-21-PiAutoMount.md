---
layout:      post
title:       "取消树莓派3B+自动挂载usb设备"
subtitle:    "------官方图形化界面"
date:        2020-05-21
author:      "Boy's Zhang"
header-img:  "img/post-bg-ps4-witcher3.jpg"
tags:
  - 软路由
  - 树莓派
---


> 之前一直用命令行界面，上次由于想把树莓派用做日常看视频机器，故安装了图形化界面。

### 取消自动挂载usb设备

自动挂载只有以pi用户登录的图形化界面下才会出现


+ 首先确定你是开启了树莓派的图形化操作界面（我是安装的中文）
+ 在图形化界面中，打开文件管理器（file manager）

![1](/img/in-post/2020-05-21-PiAutoMount/1.png)



+ 依次打开编辑-> 偏好设置

![2](/img/in-post/2020-05-21-PiAutoMount/2.png)


+ 点击卷管理,根据自己的需要取消，我这里由于需要手动挂载故都取消了

![3](/img/in-post/2020-05-21-PiAutoMount/3.png)


+ 如果不想桌面显示插入的usb设备，点击布局取消 Volumes  and Mounts

![4](/img/in-post/2020-05-21-PiAutoMount/4.png)


### 需要注意的点

配置更改后需重启系统才生效
操作要在pi账户下，原因是由于挂载/media 是pi账户的，挂载后的文件权限都是pi账户






