---
layout:      post
title:       "快速为nginx配置https"
subtitle:    "----Let's Encrypt "
date:        2019-10-30
author:      "Boy's Zhang"
header-img:  "img/post-bg-https.png"
tags:
  - nginx
  - https
---

----------------------------------------------------------

> 现如今https已经很普及了，所以为自己的网站部署https是很有必要的，而谈到https就要提到ca证书，现在市面上的证书有收费也有免费的，本文着重记录下如何从从Let's Encrypt获取证书。

----------------------------------------------------------------

Let’s Encrypt是一家免费开放的证书颁发机构，支持申请泛域名证书，不过免费的证书仅有3个月的有效期，所以可以使用脚本实现自动续期。

## acme自动续期

申请说明，[官网链接](https://github.com/Neilpang/acme.sh/wiki/%E8%AF%B4%E6%98%8E "申请注册")

安装acme

    curl https://get.acme.sh | sh
    wget -O -  https://get.acme.sh | sh

查看acme版本

    acme.sh --version

请填写实际key&Secret（这里使用namecheap做域名解析）

    export NAMECHEAP_USERNAME="..."
    export NAMECHEAP_API_KEY="..."
    export NAMECHEAP_SOURCEIP="https://ifconfig.me/ip"

申请证书

    acme.sh --issue --dns dns_ali -d *.example.com

更新证书

    acme.sh --renew -d '*.example.com' --force

查看证书列表

    acme.sh --list 

删除证书

    acme.sh remove <SAN_Domains>

升级 acme.sh 到最新版：

    acme.sh --upgrade

开启自动升级：

    acme.sh  --upgrade  --auto-upgrade

关闭自动更新：

    acme.sh --upgrade  --auto-upgrade  0

以下命令无需执行，据查看，acme会自动添加续期的定时任务

    crontab -e
    添加如下的任务：三个月执行一次
    0 0 29 */3 * acme.sh --renew -d '*.example.com' --force

最后请不要忘记修改nginx配置以及重启

## sslforfree

[官网链接](https://www.sslforfree.com/#tutorials "注册")

![sslforfree](/img/in-post\2019-10-30-RegisteredSSL/sslforfree.png)

注册流程非常简单，跟着一步步做就可以了，自动续期方法暂时还没有找到，以后找到了回来填个坑。

## certbot

[github地址](https://github.com/certbot/certbot "源码地址")

[官网地址](https://certbot.eff.org/ "官网链接")

### certbot-auto脚本

根据脚本提示选择即可注册安装。
注意：如服务器已启用了 https 服务，则先停止它。certbot-auto 在作验证时会使用 433 端口

下载 certbot-auto

    wget https://dl.eff.org/certbot-auto
    chmod a+x certbot-auto

执行自动安装,该命令会尝试自动配置 nginx ,你也可以使用下条命令只生成适合 nginx 使用的证书，然后手动配置 nginx

    ./certbot-auto --nginx

生成适合 nginx 使用的证书

    certbot-auto --nginx certonly

生成成功后，可以查看证书状态

    ./certbot-auto certificates

测试自动更新

    ./certbot-auto renew --dry-run

执行自动更新

    systemctl stop nginx
    certbot-auto renew
    systemctl restart nginx

查看证书状态

    ./certbot-auto certificates

### certbot

安装certbot

    sudo apt-get install certbot


- 通过验证域名txt记录来注册

    sudo certbot certonly --manual --preferred-challenge dns -d daydreamboy.win
  
根据提示一步步，然后需要在域名提供商出添加txt记录

![namecheap](/img/in-post\2019-10-30-RegisteredSSL/namecheap.png)

之后输入enter后结束

- 通过验证根目录的方式注册

    certbot certonly --webroot -w /web/www -d xxx.com

/web/www 是你网站的根目录， xxx.com 是你的域名。使用这个命令，确保可以通过你的域名访问到根目录，certbot会在你网站根目录下创建一个随机文件来验证这个域名是否归属与你。

- 自动续期

通过crontab实现

    30 3 * * * /usr/bin/certbot renew  >> /var/log/le-renew.log

###freessl
可选TRUSTAsia（一年双域名）和Let's Encrypt V2（三个月通配符多域名）
我这里部署bitwarden使用的是TRUSTAsia（一年双域名），部署我这里选择的也是txt域名验证
[官网地址](https://freessl.cn/ "官网地址")


## nginx配置

安装等步骤略过

    /etc/nginx/sites-enabled/my.conf(此文件为自建)  或者/etc/nginx/nginx.conf
    (此配置为比较老版本的nginx)
    server {
    listen 443 ;

    server_name example.com;	
    
    ssl on;
    ssl_certificate /etc/nginx/ca/fullchain.pem;   （此目录为我的证书存放位置）
    ssl_certificate_key /etc/nginx/ca/privkey.pem;
    
    ssl_session_timeout 5m;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;  
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2 ;
    ssl_prefer_server_ciphers on;   
    location / {
              proxy_pass http://192.168.1.101:3456/;
              proxy_redirect off;
              proxy_set_header Host $host;
              proxy_set_header X-Real-IP $remote_addr;
              proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

          } 
  
    }



