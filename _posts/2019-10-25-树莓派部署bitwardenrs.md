---
layout:      post
title:       "树莓派部署bitwardenrs"
subtitle:    "----家庭密码中心"
date:        2019-10-25
author:      "Boy's Zhang"
header-img:  "img/post-bg-bitwarden.jpg"
tags:
  - linux
  - 树莓派
---

-----------------------------------------

接触了这么多的密码管理器，最早入门开始使用的是开源的 KeePassX，整体的数据库是以文件的形式存放，同步过程可支持各种云服务器和WebDAV，使用过程中感觉不太好的就是界面比较丑，chrome插件和andrion客户端用的也不太好，我是用keepassX+onedrive实现全平台的同步。使用keepass唯一一点就是数据是在自己手上，比较放心。


今年年初的时候入手了一个树莓派，然后就想着折腾折腾，无意中发现了bitwarden这个项目，当时最吸引我的几点是：这个项目的几个客户端体验都做得非常出色，自动填充功能很流畅；并且支持一次性密码（OTP 二次验证）。主要可以自建服务端。

当时研究了一下，发现有几个缺点缺点：由于 Bitwarden 服务器使用 .Net 开发，如果使用 Docker 来部署，镜像体积过大；此外它使用 MSSQL 数据库，部署这个数据库对服务器要求比较高。而树莓派性能有限，我当时还部署了其他一些服务。

直到后来在 GitHub 上搜索时发现有人用 Rust 实现了 Bitwarden 服务器，项目叫做 bitwarden_rs，并且提供了 Docker 镜像。这个实现更进一步降低了对机器配置的要求，并且 Docker 镜像体积很小，部署非常方便。此外，官方服务器中需要付费订阅的一些功能，通过自建服务端得以免费实现。故记录一下部署过程。

-----------------------------------


# 安装Bitwarden服务器

## 安装Docker

具体安装方法可以看《linux上快速安装docker》。


## 准备bitwarden环境

假设你准备在主目录中存放数据，新建一个目录：
```
    cd ~ && mkdir bitwarden && cd bitwarden
    pwd
    # 应当输出 /home/username/bitwarden
```
准备一个配置文件：
```
    cat >> config.env <<EOF
    SIGNUPS_ALLOWED=true 
    DOMAIN=https://example.com 
    DATABASE_URL=/data/bitwarden.db 
    ROCKET_WORKERS=10
    WEB_VAULT_ENABLED=true 
    EOF
```
以上配置文件的说明：

+ SIGNUPS_ALLOWED 控制是否开放用户注册，因为你必须先注册才能存储数据，所以暂且先打开；
+ DOMAIN 填入你准备分配给 Bitwarden 服务使用的域名；
+ DATABASE_URL 设置数据库在容器内的路径，你可以不设置，默认位于 /data/db.sqlite3；
+ ROCKET_WORKERS 设置服务器使用几个线程。10 是默认值，你可以根据机器性能和个人需求适当调整；
+ WEB_VAULT_ENABLED 设置是否开启 Web 客户端。如果开启，可以通过访问你的域名来打开 Web 客户端，用户登录后即可通过网页管理密码。因为注册用户需要，所以也暂且先打开；

准备服务描述文件：
```
    cat >> docker-compose.yml <<EOF
    version: '3'
    services:
    bitwarden: 
    image: bitwardenrs/server:raspberry 
    container_name: bitwarden
    restart: always
    volumes:
    - ./data:/data 
    env_file:
    - config.env
    ports:
    - "6666:80" 
    EOF
```
这个文件主要描述了这些内容：


+ bitwarden 现在是唯一一个服务；
+ image: bitwardenrs/server:raspberry 指定使用 Docker Hub 的 bitwardenrs/server 树莓派镜像；
+ volumes 中指定将容器内的 /data 目录挂载到宿主机的当前目录下的 data 目录，这样你可以在宿主机上执行数据库的备份操作；
+ ports 指定将容器内的 80 端口映射到了宿主机的 6666 端口；

以后对 bitwarden 服务做的所有操作，都需要预先进入这两个配置文件所在的目录内。

## 反向代理


我这里选用了nginx做反向代理的工具，首先安装nginx

    sudo apt-get update
    sudo apt-get install nginx
    sudo systemctl restart nginx
    sudo systemctl enable nginx

具体配置如下

+ 创建单独的配置文件

    /etc/nginx/sites-enabled/my.conf
    或者
    /etc/nginx/conf.d/my.conf

+ 增加配置项

```
    server {
	listen 80 ;

	server_name example.com;	 #换成你自己的域名，需要到域名服务商修改对应的dns记录
	location / {
            proxy_pass http://192.168.1.101:6666/;   #修改为bitwarden的ip和端口
            proxy_set_header Host $host;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

        } 
 
	}

```

## 配置HTTPS

这方面的配置可以参考《快速为nginx配置https》。


## 关闭用户注册、关闭 web vault

现在你的 Bitwarden 服务器允许任何人注册帐号使用，你可能希望关闭这个功能。在前面生成的 config.env 中，调整以下两项值：

    SIGNUPS_ALLOWED=false
    WEB_VAULT_ENABLED=false

修改之后，需要重启 bitwarden 服务才生效。运行以下命令来删除并重新创建容器。不必担心，因为指定了 volume 映射，你的数据不会被删除。

    docker-compose down && docker-compose up -d

这样就关闭了用户注册功能，并禁用了 web vault 的访问。密码数据之后还是可以在客户端中进行编辑的。

## 遇到的问题


+ andrion与ios端登录不上（“发生错误 There is a problem connecting to the server” 错误）：

```
    经尝试更换https证书，关闭系统代理等登陆均未修复，后尝试放行http服务即可登陆

```


+ 内网pc端登陆（“Failed to fetch” errors on desktop app）：

```

    登录地址为 https://192.168.1.100/

    经尝试更换https证书，关闭系统代理等登陆均未修复，后尝试放行http服务，将地址改为http://192.168.1.100后修复
```

andrion与ios端登陆问题，可能为https证书导致，后续尝试更换证书提供商。
