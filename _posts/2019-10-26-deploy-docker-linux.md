---
layout:      post
title:       "linux上快速安装docker"
subtitle:    "----安装与简单使用"
date:        2019-10-26
author:      "Boy's Zhang"
header-img:  "img/post-bg-docker.jpg"
tags:
  - linux
  - docker
---


> docker作为一个轻量化的容器经常被使用，现总结了三种安装docker的方法。



+ 方法一(脚本)：

跑脚本之前可以通过
```
  apy-get update/apt-get upgrade
```
来进行更新，更新后通过官方脚本进行安装。
```
  curl -sSL https://get.docker.com/ | sh
```
+ 方法二（参考 docker-compose 的官方文档来安装）：
```
  sudo curl -L https://github.com/docker/compose/releases/download/1.22.0/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
```
+ 方法三（apt-get/yum安装）：

（此示例为树莓派上安装docker,并不是所有的步骤都需要）

设置树莓派国内源

1、查看树莓派版本

```shell
pi@raspberrypi:~ $ lsb_release -a
No LSB modules are available.
Distributor ID:	Raspbian
Description:	Raspbian GNU/Linux 10 (buster)
Release:	10
Codename:	buster
```


2、使用管理员修改/etc/apt/sources.list

```shell
sudo cp /etc/apt/sources.list /etc/apt/sources.bak

sudo vim /etc/apt/sources.list

删除所有内容或者注释所有内容

deb http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ buster main contrib non-free rpi
deb-src http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ buster main contrib non-free rpi

根据自己系统版本修改源中的版本
```

3、使用管理员权限，编辑/etc/apt/sources.list.d/raspi.list文件

```shell
sudo cp /etc/apt/sources.list.d/raspi.list /etc/apt/sources.list.d/raspi.bak

sudo vim /etc/apt/sources.list.d/raspi.list

删除所有内容或者注释所有内容

deb http://mirror.tuna.tsinghua.edu.cn/raspberrypi/ buster main ui
deb-src http://mirror.tuna.tsinghua.edu.cn/raspberrypi/ buster main ui

根据自己系统版本修改源中的版本


```


4、首先需要更新一下软件包的索引。

```
  sudo apt-get update
```
5、安装 HTTPS 所依赖的包，使apt-get可以通过https进行下载
```
    sudo apt-get install apt-transport-https ca-certificates software-properties-common
```
/6、添加 Docker 的 GPG key
```
  curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -
```
7、设置docker源
```shell
echo "deb [arch=armhf] https://download.docker.com/linux/debian \
     $(lsb_release -cs) stable" | \
    sudo tee /etc/apt/sources.list.d/docker.list
```


或者sudo vim /etc/apt/sources.list
```
添加一行：
```
    deb https://apt.dockerproject.org/repo/raspbian-RELEASE main
```
根据自己系统版本调整上面的 RELEASE。通过下面的命令可以查看发行版。
```
    lsb_release -cs
```
8、安装 Docker
```
    sudo apt-get update
    sudo apt-get -y install docker-engine
```
测试 Docker
运行 hello-world 镜像来做一个测试。
```
    sudo docker run hello-world
```
如果 Docker 安装成功，你会看到一条消息：“Hello from Docker!”。