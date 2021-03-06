---
layout:      post
title:       "RHCE考试总结"
subtitle:    "----题目及答案分享"
date:        2019-10-18
author:      "levi"
header-img:  "img/post-bg-redhat.jpg"
tags:
  - linux
---
# RHCE7考试题目总结

**一、题库及答案分享**

RHCE考试：

前期准备：

    ip a 查看IP地址 
    cat /etc/resolv.conf查看DNS 
    hostname查看主机名 
 

配置yum ： 

    vim /etc/yum.repos.d/server.repo
    [base]
    name=redhat
    baseurl=file:///mnt  考试时根据给的说明写相应的地址（类似http：//……） 
    enabeld=1
    gpgcheck=0
 
-----------------------------------------------------------------------
 
1.selinux

SElinux有三种模式，请将system1与system2运行于强制模式 

    #getenforce

    #setenforce 1

    #vim /etc/selinux/config
    设置为enforcing

------------------------------------------------------------------------------------------
 
2.配置SSH 

用户能够从域rhce.cc内的客户端通过SSH访问您的两个虚拟机系统 
在域my133t.org内的客户端不能访问您的两个虚拟机系统 


    #vim /etc/hosts.allow

    添加sshd : 192.168.122.0/255.255.255.0  (：前后都存在空格，且不支持/24这种格式的掩码书写方式)
    #vim /etc/hosts.deny
    添加sshd : .my133t.org  (考试可写域名也可写网段)

    #scp /etc/hosts.allow /etc/hosts.deny system2.rhce.cc:/etc  (拷贝到system2上去)
 
------------------------------------------------------------------------------------------
 
3.命令别名、自定义用户环境

在系统system1和system2上创建自定义命令为qstat,此自定义命令将执行/bin/ps -Ao pid,tt,user,fname,rsz 此命令对系统中所有用户有效 。

    #vim /etc/bashrc
    添加alias qstat='/bin/ps –Ao pid,tt,user,fname,rsz'
    #source /etc/bashrc

* 输入qstat 能看到相应输出即代表配置完成。

* scp /etc/bashrc system2:/etc/ 拷贝到system2上

* 然后进入systeem2，输入source /etc/bashrc，再输入qstat查看结果
 
------------------------------------------------------------------------------------------
 
4.端口转发 
在system1上配置端口转发，在192.168.122.0/24中的系统，访问system1的本地端口5423将被转发到80，此设置永久生效。


    firewall-cmd –list-rich-rules （查看是否有规则，没有的话继续）

    firewall-cmd --add-rich-rule 'rule family=ipv4 source address=192.168.122.0/24 forward-port port=5423 protocol=tcp to-port=80'  （添加转发规则）

    firewall-cmd --add-rich-rule 'rule family=ipv4 source address=192.168.122.0/24 forward-port port=5423 protocol=tcp to-port=80' --permanent （让其永久执行）

如记不住如何书写，可通过man -k firewall

    [root@esunny ~]# man -k firewall
    firewall-cmd (1)     - firewalld command line client
    firewall-offline-cmd (1) - firewalld offline command line client
    firewalld (1)        - Dynamic Firewall Manager
    firewalld.conf (5)   - firewalld configuration file
    firewalld.dbus (5)   - firewalld D-Bus interface description
    firewalld.direct (5) - firewalld direct configuration file
    firewalld.helper (5) - firewalld helper configuration files
    firewalld.icmptype (5) - firewalld icmptype configuration files
    firewalld.ipset (5)  - firewalld ipset configuration files
    firewalld.lockdown-whitelist (5) - firewalld lockdown whitelist configuration file
    firewalld.richlanguage (5) - Rich Language Documentation
    firewalld.service (5) - firewalld service configuration files
    firewalld.zone (5)   - firewalld zone configuration files
    firewalld.zones (5)  - firewalld zones

    [root@esunny ~]# man firewalld.richlanguage

查看example进行配置
    
------------------------------------------------------------------------------------------------------------
 
5.配置聚合链路

在system1和system2 之间配置链路聚合 
此链路使用接口eth1和eth2 
此链路在一个接口失效后，仍然能工作 
此链路在system1上使用地址172.16.11.25/255.255.255.0 
此链路在system2上使用地址172.16.11.35/255.255.255.0
此链路在系统重启后依然保持正常状态


    system1：

    cd /usr/share/doc/teamd/example_ifcfgs/1

    cp * /etc/sysconfig./network-scripts/

    修改ifcfg-team_test0

    DEVICE="team_test0"
    NAME="team_test0"
    DEVICETYPE="Team"
    ONBOOT="yes"
    BOOTPROTO=none
    NETMASK=255.255.255.0
    IPADDR=172.16.11.25
    TEAM_CONFIG='{"runner": {"name": "activebackup"}}'

    修改ifcfg-eth1

    DEVICE="eth1"
    NAME="eth1"
    DEVICETYPE="TeamPort"
    ONBOOT="yes"
    TEAM_MASTER="team_test0"

    修改ifcfg-eth

    DEVICE="eth2"
    NAME="eth2"
    DEVICETYPE="TeamPort"
    ONBOOT="yes"
    TEAM_MASTER="team_test0"
 
    重启网络systemctl restart network

    scp ifcfg-team_test0 ifcfg-eth1 ifcfg-eth2 root@system2:/etc/sysconfig/network-scripts/

    修改为对应ip重启网络后生效
    通过ping 进行测试连通性

------------------------------------------------------------------------------------------------------------
 
6.配置IPv6地址

在您的考试系统上配置接口,在你的默认网卡上使用如下IPv6地址，system1上的IP地址应该是200e:ac18::e0a/64，system2上的IP地址应该是200e:ac18::e14/64，两个系统必须能与网络200e:ac18/64内的系统通信，地址必须在重启后依然生效，两个系统保持当前的IPv4地址并能通信。

    system1:

    #nmcli connection show eth0 | grep ipv6(查看下ipv6信息)

    #nmcli connection modify eth0 ipv6.method auto （先行做下防止出错）
    #nmcli connection modify eth0 ipv6.addresses "200e:ac18::e0a /64" 
    #nmcli connection modify eth0 ipv6.method manual

    #systemctl restart network
    systemctl enable network

    system2:

    #nmcli connection modify eth0 ipv6.method auto （先行做下防止出错）
    nmcli connection modify eth0 ipv6.addresses "200e:ac18::e14/64" 
    nmcli connection modify eth0 ipv6.method manual
    systemctl restart network
    systemctl enable network
    测试： 
    ping6 200e:ac18::e14

    如果不通的话可以使用如下命令：

    nmcli con up eth0 或者 ifdown eth0 \ifup eth0

    nmcli device disconnect eth0 \ nmcli device connect eth0 
 
--------------------------------------------------------------------------------------------------------------------------------
 
7.配置本地邮件服务器

在系统system1和system2上配置邮件服务----postfix,这些系统不接受外部发来的邮件,在这些系统上本地发送任何邮件都会被路由到rhgls.rhce.cc,从这些系统上发送的邮件显示来自于rhce.cc,您可以通过发送邮件到本地用户 'dave' 来测试您的配置， 系统 rhgls.rhce.cc 已经配置把此用户的邮件转到下列 URL http://rhgls.rhce.cc/received_mail/11

    #rpm -qa postfix （查看是否安装邮件服务）


    vim /etc/postfix/main.cf
    复制mydestination =xxxxxxx  （在165行）
    粘贴后修改为mydestination=  (=空白，这样本地就不能收任何邮件了)
    往下找到relayhost =xxxx

    复制修改为relayhost=[rhgls.rhce.cc] (本地发送任何邮件都会被路由到rhgls.rhce.cc)

    找到myorigin=xxxx

    复制修改为myorigin=rhce.cc  (系统上发送的邮件显示来自于rhce.cc)
    保存退出 
    #systemctl restart postfix

    #systemctl enable postfix

    #id dave （查看是否有dave这个用户信息）



    在系统1上测试发送邮件：echo aaaa|mail –s “test” dave  //’test’改为bbb aaa都行

    如果提示没有该命令，需要按照mail插件

    #yum –y install mailx 

    在物理机上
    firefox http://host.rhce.cc/received_mail/ & 通过浏览器来访问查看dave的邮件信息，打开有个to dave的即可

    在system2上实现：

    #scp /etc/postfix/mail.cf system2.rhce.cc:/etc/postfix/

    #system restart postfix

    如果没有mailx的话同样yum安装即可

    #echo aaa11111 | mail –s sss dave


    也可以用下面方法来查看 
    wget http://host.rhce.cc/received_mail/dave
    cat /var/spool/mail/dave 来查看用户dave的邮件信息


 
---------------------------------------------------------------------------------------------
 
8.通过SMB共享目录

在system1上配置SAMBA服务，您的samba服务器必须是STAFF工作组的一个成员，共享/common目录，共享名为common 
只有rhce.cc域内的客户端可以访问common共享，Common必须是可以浏览的，用户andy必须能够读取共享中的内容，如果需要的话，验证密码是redhat。
 
    system1:
    #yum -y install samba cifs*

    #vim /etc/samba/smb.conf
    workgroup = STAFF                                     //在89行中修改

    最后再添加如下信息

    [common]

    path = /common

    hosts allow= 192.168.122.0/24

    保存退出

    #mkdir /common    //创建common文件夹，在根下创建

    #chcon -R -t samba_share_t /common/             //更改上下文 一定要修改！
    #systemctl restart smb   //别忘了重启服务

    #systemctl enable smb  

    #id andy                                                       //查看andy用户，没有该用户的话就创建
    #useradd andy

    #yum -y install samba-client 安装SMB密码插件

    #smbpasswd –a andy                             //创建验证密码，输入密码redhat两次

    #firewall-cmd --add-service=samba

    #firewall-cmd --add-service=samba --permanent

    #pdbedit –L  //列出Samba用户列表

    

    system2上验证：
    #yum -y install samba-client cifs*           //安装smb客户端
    #smbclient -L //system1.rhce.cc –U andy%redhat       //查看共享目录

    或者#smbclient //system1.rhce.cc/common -U andy%redhat           //浏览共享目录


------------------------------------------------------------------------------------------------------------
 
9.配置多用户samba挂载 
在system1上通过SMB共享目录/ miscellaneous 满足以下要求：
共享名为 miscellaneous 
共享目录 miscellaneous 只能被rhce.cc域内的客户端使用 
共享目录 miscellaneous 必须可以被浏览 
用户 silene 能以读的方式访问此共享，访问密码是redhat 
用户 akira  能以读写的方式访问此共享，访问密码是redhat 
此共享永久挂载在system2上的/mnt/multi目录，并使用用户 silene 进行认证，任何用户可通过 akira 来临时获得读写权限

    因为在上面那题中在防火墙中已经允许了samba服务，所有这里就不再操作了
    system1:
    #mkdir /miscellaneous   //在根下创建
    #chcon –R -t samba_share_t  /miscellaneous  //更改上下文，

    #vim /etc/samba/smb.conf                                          //编辑samba配置文件
    [miscellaneous]
    path = /miscellaneous

    hosts allow=192.168.122.0/24

    valid users = silene,akira   //可不要
    writable = no 
    write list = akira
    保存退出


    #id silene  //查看是否用户已创建

    #id akira
    #useradd silene
    #useradd akira

    #smbpasswd -a silene                                                        //修改samba访问用户密码
    #smbpasswd -a akira                                                          //修改samba访问用户密码

    #chcon –R -t samba_share_t  /miscellaneous  //更改上下文，先更改上下文，再赋予其他人权限！切记

    #chmod o+w  /miscellaneous    //赋予其他人可以写入的权限

    然后#ls –ldZ /miscellaneous 查看下其他人的权限是否有

    #systemctl restart smb.service

    #systemctl enable smb.service
    
    进入system2:
    #yum -y install samba-client cifs*  //如果第8题安装完了的话就不必再安装了
    #mkdir /mnt/multi                    
    #vim /etc/fstab    //永久挂载
    //system1.rhce.cc/miscellaneous  /mnt/multi  cifs defaults,multiuser,sec=ntlmssp,username=silene,password=redhat  0 0
    保存退出
    mount -a 看能否挂载上  //如果挂不上看下服务是否启动

    验证：
    useradd tom1                                                              //system2添加tom1用户
    su –  tom1                                                                       //切换到tom1用户下
    #cifscreds add 192.168.122.100 -u akira                            //获取服务器端用户权限

    或者# cifscreds add system1.rhce.cc -u  akira
    cd /mnt/multi
    touch aaaa  //可以创建说明成功,如果无法创建，一般是更改下上下文，然后修改下共享文件夹的其他用户o+w权限就OK了~

    

    也可以通过#smbclient -L //192.168.122.100/ -U silene 来查看共享目录

    或者#smbclient //system1.rhce.cc/common -U silene%redhat          //浏览共享目录


 
------------------------------------------------------------------------------------------------------------


10.配置NFS服务

在system1上配置NFS服务 
以只读的方式共享 /public，同时只能被rhce.cc内系统访问 
以读写的方式共享 /protected 能被 rhce.cc 内用户访问 
访问 /protected 需要通过 Kerberos 安全加密，您可以使用下边 URL链 接提供的秘钥：http://host.rhce.cc/materials/nfs_server.keytab
目录 /protected 应该包含名为 confidential 拥有人为 ldapuser11 的子目录 
用户 ldapuser11 能以读写的方式访问 /protected/confidential 
 
system1配置： 
添加富规则（防火墙先开放下面三个服务）：

    # firewall-cmd --add-rich-rule 'rule family=ipv4 source address=192.168.122.0/24 service name=nfs accept'     //添加nfs富规则

    # firewall-cmd --add-rich-rule 'rule family=ipv4 source address=192.168.122.0/24 service name=nfs accept' --permanent  //永久生效

    # firewall-cmd --add-rich-rule 'rule family=ipv4 source address=192.168.122.0/24 service name=rpc-bind accept'    添加rpc-bind富规则

    #firewall-cmd --add-rich-rule 'rule family=ipv4 source address=192.168.122.0/24 service name=rpc-bind accept' --permanent  //永久生效

    # firewall-cmd --add-rich-rule 'rule family=ipv4 source address=192.168.122.0/24 service name=mountd accept'    //添加mountd富规则

    #firewall-cmd --add-rich-rule 'rule family=ipv4 source address=192.168.122.0/24 service name=mountd  accept' --permanent //永久生效

    yum -y install sssd* authconfig* krb5* //不用
    #mkdir /public

    #mkdir /protected
    #vim /etc/exports  //添加例外，内容修改为：注意：是exports,不是export
    /public      192.168.122.0/24(ro,sync)   //只读 
    /protected    192.168.122.0/24(rw,sync,sec=krb5p)  //读写并通过kerbros加密
    保存退出

    # wget -O /etc/krb5.keytab http://host.rhce.cc/materials/nfs-server.keytab //-O为另存为的意思 
    #vim /etc/sysconfig/nfs     //修改配置文件NFS格式
    RPCNFSDARGS="-V 4.2"     //第13行,下面system2里挂载的时候格式也应该为v4.2
    保存退出

    # mkdir  /protected/confidential   //创建文件夹confidential

    # chown ldapuser11 /protected/confidential  //使其拥有人为 ldapuser11

    #chcon –R –t public_content_t /protected/confidential  //修改上下文，因为拥有人都已经是ldapuser11了，所以其肯定有读写权限，修改下上下文即可



    #systemctl start  nfs-secure-server  //此为服务端的
    #systemctl enable nfs-secure-serve  //设置开机自动启动服务

    #systemctl start  nfs-server  //此为服务端的
    #systemctl enable nfs-serve  //设置开机自动启动服务
  
    验证：

    #system1:exportfs -avr

    system2上：
    showmount -e system

-------------------------------------------------------------------------------------------------------
 



11.挂载一个NFS共享

在system2上挂载一个来自于system1.rhce.cc的NFS共享，并符合下列要求：
/public挂载在下面的目录上 /mnt/nfsmount
/protected挂载在下面的目录上 /mnt/nfssecure ，并使用安全的方式，秘钥下载地址http://host.rhce.cc/materials/nfs_client.keytab  
用户ldapuser11能在 /mnt/nfssecure/confidential 上创建文件 
这些文件系统在系统启动时自动挂载

    system2上的配置：

    #showmount –e system1.rhce.cc  //先看下是否有共享的目录，有的话继续，没的话需要看下上一题~
    yum -y install krb5* //不用

    #mkdir /mnt/nfsmount     //创建将要共享的文件

    # mkdir /mnt/nfssecure
    #wget -O /etc/krb5.keytab http://host.rhce.cc/materials/nfs-client.keytab
    #vim /etc/fstab   //永久挂载，添加内容如下：
    system1.rhce.cc:/public /mnt/nfsmount     nfs   defaults      0  0
    system1.rhce.cc:/protected  /mnt/nfssecure    nfs   defaults ,sec=krb5p, v4.2  0 0   //别忘了服务器分享端的格式为v4.2这里要上下文对应起来
    保存退出

    #mount –a  //刷新挂载

    #systemctl start nfs-secure //此为客户的的，如果挂载不是就重启下该服务
    #systemctl enable  nfs-secure

    # df –hT    //查看下是否挂载上


    测试： 在system1上ssh 到system2上去

    #ssh ldapuser11@system2.rhce.cc 
    #cd  /mnt/nfssecure/confidential
    #touch haha

    #ls  //有显示haha文件就证明OK
    #exit  //退出
 
-------------------------------------------------------------------------------------------------------------------------------
 
12.配置WEB站点

在system1 上配置一个web站点 http://system1.rhce.cc，然后执行下述步骤：
从 http://rhgls.rhce.cc/materials/station.html 下载文件，并重命名为index.html，

不要修改文件内容。 
将文件 index.html 拷贝到您的web服务器的 DocumentRoot 目录下 
来自于 rhce.cc 域的客户端可以访问该web服务器 
来自于 my133t.org 域的客户端拒绝访问此web服务  
    
    system1上配置： 
    #yum groupinstall –y web*  //也可以不安装web* 只安装http\mod_ssl\mod_wsgi即可~

    #vi /etc/ httpd/conf/httpd.conf

    在配置中找到serverName一项，修改内容如下：

    ServerName  system1.rhce.cc:80    //配置web站点
    保存退出

    #wget  -O  /var/www/html/index.html  http://rhgls.rhce.cc/materials/station.html

    //-O另存为  第一个为存储文件 第二个为下载地址
    #firewall-cmd --add-rich-rule 'rule family=ipv4  source address=”192.168.122.0/24” service name=http accept'   //设置防火墙允许其（192.168.122.0）访问

    #firewall-cmd --add-rich-rule 'rule family=ipv4  source address=”192.168.122.0/24” service name=http  accept'--permanent

    #system strart httpd

    #system enable httpd
    system2上验证：

    #curl –s http://system1.rhce.cc

    或者
    #firefox http://system1.rhce.cc  显示station即可~
    注意：如果打不开的话需要去看下#cat /etc/resolv.conf 文件里面的nameserver是否为192.168.122.10 考试时不用考虑，测试环境需要注意 
    或者在宿主机上通过firefox打开http://system1.rhce.cc

------------------------------------------------------------------------------------------------------------
 
13. 配置安全web服务(跟14题有关系，有些就一起做了)

为站点 http://system1.rhce.cc 配置 TLS 加密 
一个已签名证书从 http://host.rhce.cc/materials/system1.crt 获取 
此证书的秘钥从 http://host.rhce.cc/materials/system1.key 获取 
此证书的签名授权信息从 http://host.rhce.cc/materials/domain11.crt 获取 
    
    system1上的配置： 
    #yum -y install mod_ssl （如之前执行过yum groupinstall web*则不需要重复安装）
       

    #vim /etc/httpd/conf.d/ssl.conf

    修改：

    DocumentRoot "/var/www/html"
    ServerName system1.rhce.cc:443

    SSLCertificateFile /etc/httpd/conf.d/system1.crt

    SSLCertificatekeyFile /etc/httpd/conf.d/system1.key

    SSLCertificateChainFile /etc/httpd/conf.d/domain11.crt

    保存退出



    #cd /etc/httpd/conf.d/   //CD到要下载文件的目录中，跟上面vhosts的路径吻合起来
    #wget http://host.rhce.cc/materials/system1.crt   
    #wget http://host.rhce.cc/materials/system1.key

    #wget http://host.rhce.cc/materials/domain11.crt


    #systemctl restart httpd  //重启服务



    如果出现网页打不开，需要做下如下操作，把https防火墙给开了 (注意，网页上的服务是https,不是httpd! )


    修改富规则，如下：（推荐！）

    #firwall-cmd --add-rich-rule 'rule family=ipv4  source address=192.168.122.0/24 service name=https accept' 
    #firwall-cmd --add-rich-rule 'rule family=ipv4  source address=192.168.122.0/24 service name=https accept' --permanent


    system2：验证： 
    #firefox https://system1.rhce.cc  

------------------------------------------------------------------------------------------------------------
 
14、配置虚拟主机

在 system1 上扩展您的WEB服务器为站点 http://www.rhce.cc 创建一个虚拟主机，然后执行下述步骤：
设置 DocumentRoot 为 /var/www/virtual 
从 http://rhgls.rhce.cc/materials/www.html 下载文件，并重命名为index.html，不要修改该文件内容。 
将文件 index.html 放到DocumentRoot 目录下 
确保 andy 用户能够在 /var/www/virtual 目录下创建文件 
注意：原站点 system1.rhce.cc 必须仍然能够访问 ，名称服务器 rhce.cc 提供对主机名 www.rhce.cc 的域名解析


    system1上配置：
    copy模板文件
    #cp /usr/share/doc/httpd-2.4.6/httpd-vhosts.conf /etc/httpd/conf.d/vhosts.conf  //拷贝文件过来
    #vim /etc/httpd/conf.d/vhosts.conf  //配置虚拟主机信息
    空白行开始（其余没注释的全部删除）
     
    #mkdir  /var/www/virtual //创建文件夹

    #wget -O  /var/www/virtual/index.html  http://rhgls.rhce.cc/materials/www.html

    创建虚拟站点：

    #vi /etc/httpd/conf.d/vhost.conf  

    <VirtualHost www.rhce.cc:80>
        DocumentRoot  /var/www/virtual
        ServerName  www.rhce.cc
    </VirtualHost>

    <VirtualHost system1.rhce.cc:80>
        DocumentRoot  /var/www/html
        ServerName  system1.rhce.cc
    </VirtualHost>

    <Directory "/var/www/html">
        AllowOverride None
        Require all granted
    </Directory>

    <Directory "/var/www/virtual">
        AllowOverride None
        Require all granted
    </Directory>


    

    保存退出，其中最下面的路径Directory是从#vim /etc/httpd/conf/httpd.conf模板中提取的，关键词**

    #system restart httpd  //重启服务

    

    #id andy   //查看是否有andy用户

    如果没有用#useradd andy添加

    #ls –ld /var/www/virtual //查看下是否其他用户有权限修改该文件夹，不能的话执行下面操作：
    setfacl -m u:andy:rwx /var/www/virtual  //对andy用户可读可写可执行加权
    systemctl restart httpd
    客户端system2进行验证

    #curl –s www.rhce.cc

    或者
    #firefox www.rhce.cc 输出www   //此为最新添加的虚拟站点
    注意点：

    vhost是放在/etc/httpd/conf.d目录里面的，不是conf！！！


------------------------------------------------------------------------------------------------------------
 
15.配置web内容的访问 
在您 system1上的 web 服务器的 DocumentRoot 目录下创建一个名为 secret 的目录

要求如下：
从 http://rhgls.rhce.cc/materials/private.html 下载一个文件副本到这个目录，并重命名为index.html，不要对这个文件文件内容做任何修改 
从 system1 上，任何人都可以浏览 secret 的内容，但是从其他系统不能访问这个目录的内容 
    
    system1配置：

    #mkdir  /var/www/html/secret

    #mkdir  /var/www/virtual/secret //为防止出错，在html和www里面均创建secret文件

    #wget -O http://rhgls.rhce.cc/materials/private.html/var/www/html/secret/index.html

    #wget -O http://rhgls.rhce.cc/materials/private.html/var/www/virtual/secret/index.html
    #vim /etc/httpd/conf.d/vhosts.conf  //复制最后4行粘贴2次添加修改内容如下：


    <Directory "/var/www/html/secret">
        AllowOverride None
        Require local   //这里local的意思是只允许自己访问
    </Directory>

    <Directory "/var/www/virtual/secret">
        AllowOverride None
        Require local   //这里local的意思是只允许自己访问
    </Directory>
  


    

    保存退出
    #systemctl restart httpd  //重启服务
    验证：
    system1访问

    #curl –s http://system1.rhce.cc/secret/index.html 正常的话应该显示private

    #curl –s http://www.rhce.cc/secret/index.html    正常的话应该显示private 
    system2访问

    #curl –s http://system1.rhce.cc/secret/iindex.html   应该被拒绝显示为forbidden

    #curl –s http://www.rhce.cc/secret/index.html  应该被拒绝显示为forbidden


------------------------------------------------------------------------------------------------------------
 
16.实现动态web内容 （一般不考这题）
在system1上配置提供Web内容，要求如下：

动态内容由名为 dynamic.rhce.cc 的虚拟主机提供 
虚拟主机侦听端口为 8998
从 http://rhgls.rhce.cc/materials/webapp.wsgi下载一个脚本，然后放在适当的位置，不要修改文件内容 
客户端访问 http://dynamic.rhce.cc:8998 时，应该接收到动态生成的web页面 
此 http://dynamic.rhce.cc:8998 必须能被rhce.cc内所有的系统访问 
    
    system1配置： 
    #vim /etc/httpd/conf.d/vhosts.conf //添加内容如下，其中有些结构直接复制上面的即可
    <VirtualHost dynamic.rhce.cc:8998>
        DocumentRoot  /var/www/html
        ServerName  dynamic.rhce.cc
    </VirtualHost>

    <Directory "/var/www/virtual">
        AllowOverride None
        Require all granted
        WSGIScriptAlias  / /var/www/html/webapp.wsgi //访问/的时候就访问后面的文件
    </Directory>


    #vim /etc/httpd/conf/httpd.conf  //监听端口，在里面直接搜索/Listen/c，在Listen 80处复制添加一个8998端口，在下面直接添加内容：

    Listen 8998

    保存退出

    修改上下文，这个可以直接找模板来操作：

    #vim /etc/ssh/sshd_config //找到关键词semanage port -a –t ssh_port_t –p –t tcp

    然后直接操作刚刚复制的东西
    #semanage port -a -t http_port_t -p tcp 8998     ///原ssh改为http，添加8998端口访问（修改上下文），注意：如果此时提示未找到该命令的话，需要yum安装下：

    #yum whatprovides */semanage

    #yum install –y policyconreutils-python-2.2.5-11.el7.x86_64

    安装完继续执行上述命令。

    #yum install -y mod_wsgi  或者*wsgi*    ///安装网页服务器网关接口服务（wsgi服务）

    下面是下载文件:

    #cd /var/www/html  //这个地址是跟上面的Vhosts内容的路径对应的

    wget -P /var/www/html/  http://rhgls.rhce.cc/materials/webapp.wsgi  //-O必须指明下载到哪个路径的文件名是多少，-P只需要知道下载路径的文件夹即可即可 

    下载完后然后在/etc/httpd/conf.d/vhosts.conf中添加wsgi访问 / 即可访问该文件，在上面已黄标标明

    #ls //查看下下载文件名是多少，要与上面Vhosts的文件名一致。
    #systemctl restart httpd  //重启服务

    添加富规则：
    #firewall-cmd --add-rich-rule 'rule family=ipv4 source address=192.168.122.0/24 port port=8998 protocol=tcp accept'  //防火墙设置

    #firewall-cmd --add-rich-rule 'rule family=ipv4 source address=192.168.122.0/24 port port=8998 protocol=tcp accept' --permanent


    system2验证：

    #host dynamic.rhce.cc //看下是否能解析到该地址

    #firefox http://dynamic.rhce.cc:8998 出现UNIX EPOCH time is now:******就是成功的！ 如果出现not found则进去看看vhost文件是否修改正确
 
------------------------------------------------------------------------------------------------------------
 
17、创建一个脚本  
在system1上创建一个名为/root/script 的脚本，让其提供下列特性  
当运行/root/script foo， 输出为bar  
当运行/root/script bar，输出为foo  
当没有任何参数或者参数不是foo或者bar时，其错误输出产生以下的信息:
/root/script foo|bar 


    在system1上操作：

    #vim /root/script  //编辑脚本页如下：
    #!/bin/bash
    case $1 in

        foo)
                echo ‘bar’;;
        bar)
                echo ‘foo’;;
        *) 
                echo ‘/root/script foo|bar’

        exit 1

    esac

    保存退出

    chmod +x /root/script  //添加可执行权限
    验证：

    ./script  应该显示/root/script fool|bar

    ./script asasasa 应该显示/root/script fool|bar

    ./script foo 应该显示bar

    ./script bar 应该显示fool

 

------------------------------------------------------------------------------------------------------------



18、创建一个添加用户的脚本  
在 system1 上创建一个脚本，名为 /root/mkusers ,此脚本能实现为系统 system1创建本地用户，并且这些用户的用户名来自一个包含用户名列表的文件。同时满足下列要求：  
此脚本要求提供一个参数，此参数就是包含用户名列表的文件  
如果没有提供参数，此脚本应该给出下面的提示信息 Usage: /root/mkusers 然后退出并返回相应的值  
如果提供一个不存在的文件名，此脚本应该给出下面的提示信息 Input file not found 然后退出并返回相应的值  
创建的用户登录 shell 为 /bin/false  
此脚本不需要为用户设置密码  
你可以从下面的URL获取用户名列表作为测试用http://rhgls.rhce.cc/materials/userlist  


    在system1上操作：

    #vim /root/mkusers

    #!/bin/bash

    if [ $# -eq 0 ]; then  //没有任何参数的情况

        echo 'Usage: /root/mkusers'

        exit 1

    fi

    if [ ! -e $1 ]; then    //! -e 表示不存在，-e表示存在  或者写成-f   表示文件

        echo 'Input file not found'

        exit 1

    fi

    while read line    //如果存在的情况,随便写了个用户名变量line

    do

        useradd -s /bin/false  $line

    done < $1

    保存退出

    chmod  +x /root/mkusers
    #wget http://host.rhce.cc/materials/userlist //下载该文件到根目录
    #cat userlist   //查看下该文件有哪些用户存在

    验证：

    #./mkusers  //应该输出为Usage: /root/mkusers

    #./mkusers userlistxxxxx //随便写一个的话，应该输出为Input file not found

    #./mkusers userlist  //这个是存在的所以就创建了此文件内的所有用户


-------------------------------------------------------------------------------------------



19、配置 iSCSI 服务端
配置 system1 提供一个 iSCSI 服务 磁盘名为 iqn.2014-09.com.example.domain11:system1 ，
并符合下列要求：
服务端口为 3260
使用 iscsi_vol 作其后端卷 其大小为 3G
此服务只能被 system2.rhce.cc 访问

    system1上操作：

    ~]# fdisk -l /dev/vda    #分区

    ~]#yum install -y targetcli      #安装服务

    ~]#targetcli   

    />  ls

    />  cd backstores/

    />  cd block

    />  create  disk1 /dev/vda3

    />  cd ..

    />  cd ..

    />   cd  iscsi

    />  create iqn.2014-09.com.example.domain11:system1

    />  cd   iqn.2014-09.com.example.domain11:system1/tpg1/

    />  ls

    />  luns/ create  /backstores/block/disk1

    />  acls/  create   iqn.2014-09.com.example.domain11:xxx

    />  portals/ create 0.0.0.0 3260

    />  exit
    添加富规则：
    #firewall-cmd --add-rich-rule 'rule family=ipv4 source address=192.168.122.200/32 port port=3260 protocol=tcp accept'  //防火墙设置

    #firewall-cmd --add-rich-rule 'rule family=ipv4 source address=192.168.122.200/32· port port=3260 protocol=tcp accept' --permanent

    ~]#systemctl enaable target  #添加开机重启

    ~]#systemctl restart targe    #重启


------------------------------------------------------------------------------------------


20、配置 system2 使其能连接在 system1 的上提供的 iqn.2014-09.com.example.domain11:system1
并符合以下要求：
iSCSI 设备在系统启动的期间自动加载
块设备 iSCSI 上包含一个大小为 1700 MiB 的分区， 并格式化为 xfs
此分区挂载在 /mnt/data 上 同时在系统启动的期间自动挂载

    ~]# yum install iscsi* -y

    ~]#vim /ettc/iscsi/initiatorname.iscsi

    InitiatorName=iqn.2014-09.com.example.domain11:xxx

    ~]#iscsiadm -t st -m discovery -p system2.rhce.cc

    ~]#iscsiadm -m node -T iqn.2014-09.com.example.domain11:system1 -p system2.rhce.cc -l

    ~]#fdisk -l

    ~]#lsblk

    ~]# fdisk /dev/sda 分区

    ~]# mkfs.xfs /dev/sda1

    ~]# mkdir /mnt/data

    ~]# vim /etc/fstab

    /dev/sda1        /mnt/data  xfs    defaults,_netdev      0    0

    ~]# mount -a

------------------------------------------------------------------------------------------


21、配置一个数据库
在 system1 上创建一个 MariaDB 数据库, 名为 Contacts ， 并符合以下条件：
数 据 库 应 该 包 含 来 自 数 据 库 复 制 的 内 容 ， 复 制 文 件 的 URL 为
http://rhgls.rhce.cc/materials/users.mdb 。
数据库只能被 localhost 访问。
除了 root 用户, 此数据库只能被用户 Luigi 查询。 此用户密码为 redhat。
root 用户的密码为 redhat ， 同时不允许空密码登录。

    ~]# yum groupinstall "mariadb*" -y

    ~]# systemctl start mariadb

    ~]# systemctl enable mariadb
    
    ~]# cd /root/

    ~]# wget http://rhgls.rhce.cc/materials/users.mdb   #下载数据库


    ~]# mysql_secure_installation v   #添加root密码

    ~]# mysql -u root -p

    MariaDB > CREATE DATABASE  Contacts;

    MariaDB > use  Contacts;

    MariaDB > grant select on Contact.* to Luisi@localhost identified by 'redhat' 
set password=password('redhat')

    MariaDB > set password=password('redhat') （此为设置root密码的第二种方式，如之前设置过，此步骤忽略）

    MariaDB > source /root/users.mdb  #导入数据库

    MariaDB > flush privileges;

    MariaDB > quit

    ~]# mysql -u root -p Contacts  < /root/users.mdb   （此为第二种导入数据库方法，如使用上面步骤导入，此命令可忽略）

------------------------------------------------------------------------------------------


22、数据库查询
在系统 system1 上使用数据库 Contacts ， 并使用相应的 SQL 查询以回答下列问题：
密码是 tangerine 的人的名字?
有多少人的姓名是 John 同时居住在 Santa Clara?

    ~]# mysql -u root -p

    MariaDB > use Contacts;

    MariaDB > show tables;

    MariaDB > select * from pass inner join name where name.aid=pass.bid;

    MariaDB > select * from pass inner join name on name.aid=pass.bid where password='tangerine';

    MariaDB >select * from name inner join loc on name.aid=loc.cid where firstname='John' and loction='Santa Clara';



    如查看表中内容，可通过（desc 表名）查看表中具体字段。

