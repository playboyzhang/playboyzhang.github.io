---
layout:      post
title:       "RHCSA考试总结"
subtitle:    "----题目及答案分享"
date:        2019-10-19
author:      "Boy's Zhang"
header-img:  "img/post-bg-redhat.jpg"
tags:
  - linux
---
# RHCSA7考试题目总结

**一、题库及答案分享**

RHCSA考试：

    Hostname:station.rhce.cc
    IP address:192.168.122.100
    Netmask:255.255.255.0
    Gateway:192.168.122.1
    NameServer:192.168.122.10

前准备工作：

+ ROOT密码：redhat(考试根据考试要求设置密码)

    破解密码，并将密码设置成redhat:
    1、开机后按E ，删除rhgb quiet，写init=/bin/sh 在按Ctrl+x重启
    2、mount –o remount,rw /
    3、echo redhat | passwd –-stdin root
    4、touch /.autorelabel
    5、exec /sbin/init
    备注：考试时可以systemctl isolate graphical.target

+ 设置IP信息

    编辑网卡文本：vim /etc/sysconfig/network-scripts/ifcfg-eth0
    DEVICE=eth0
    NAME=eth0
    ONBOOT=yes
    BOOTPROTO=none
    IPADDR=192.168.122.100
    NETMASK=255.255.255.0
    GATEWAY=192.168.122.1
    DNS1=192.168.122.10

+ 设置主机名station.rhce.cc

    命令：hostnamectl set-name station.rhce.cc

-----------------------------------------------------------------------------------------

考题：
一、SELinux必须运行在Enforcing模式下

    1、查看selinux状态：getenforce
    2、把当前状态设置成enforcing：setenforce 1（重启后会失效）
    3、修改配置文件：vim /etc/selinux/config 中SELINUX=Enforcing

-----------------------------------------------------------------------------------------


二、配置YUM源，使用地址ftp://server.rhce.cc/dvd

    cd /etc/yum.reops.d 编辑文件aa.repo
    [aa]
    name=aa
    baseurl=ftp://server.rhce.cc/dvd
    enabled=1
    gpgcheck=0

    配置完成后，安装一个程序验证是否正确（例如：yum install vsftpd）

--------------------------------------------------------------


三、调整逻辑卷VO的大小，他的文件系统大小应该为290M。确保这个文件系统的内容仍然完整。注意：分区很少能精确到和要求的大小相同，因此在范围260M和320M之间都是可接受的。

    Lvscan 查询逻辑卷大小
    扩展逻辑卷大小：lvextend –L +98M /dev/vg0/vo（根据实际情况复制逻辑卷）
    查看文件系统大小：df –hT
    XFS文件系扩展：xfs_growfs /dev/vg0/vo
    EXT4文件系统扩展：resize2fs /dev/vg0/vo

---------------------------------------------------------------------------


四、创建下面的用户、组和组成员关系：
名字为adminuser的组 
用户natasha，使用adminuser作为附属组
用户harry，使用adminuser作为附属组
用户sarah，在系统上不能访问可交互的shell且不是adminuser成员，natasha/harry/sarah密码都是redhat

    groupadd adminuser
    useradd –G adminuser natasha
    useradd –G adminuser harry
    useradd –s /sbin/nologin sarah
    echo redhat | passwd --stdin natasha
    echo redhat | passwd –-stdin harry
    echo redhat | passwd –-stdin sarah

-------------------------------------------------------------------------



五、复制文件/etc/fstab到var/tmp/fstab。配置/var/tmp/fstab的权限如下：
文件/var/tmp/fstab所有者是root，属于root组
文件/var/tmp/fstab不能被任何用户执行
用户natasha可读和可写/var/tmp/fstab，用户harry既不能读也不能写
所有其他用户（现在和将来）具有读/var/tmp/fstab的能力


    复制文件：cp /etc/fstab /var/tmp/fstab
    setfacl –m u:natasha:rw- /var/tmp/fstab
    setfacl –m u:harry:--- /var/tmp/fstab


----------------------------------------------------------------------



六、用户natasha必须配置一个cron job当地时间每天14:23运行，执行：/bin/echo hiya

    crontab –e –u natasha 编辑文本： 23 14 * * * /bin/echo hiya
    检查命令：crontab –l –u natasha


----------------------------------------------------------------------



七、创建一个目录/home/admins，使之具有下面的特性：
/home/adminuser所属组为adminuser
这个目录对组adminuser的成员具有可读、可写和可执行。但是不是对其他任何用户（root可以访问系统上所有的文件和目录）
在/home/admins下创建的任何文件所属组自动设置为adminuser


    mkdir /home/admins
    chgrp adminuser /home/admins
    chmod g+w /home/admins
    chmod o-rx /home/admins/ 
    chmod g+s /home/admins


---------------------------------------------------------------------



八、从http://rhgls.rhce.cc/pub/updates安装合适的内核更新。下面的要求必须满足：
更新的内核作为系统启动的默认内核
原来的内核在系统刚启动的时候仍然可有效和可引导

    firefox http://rhgls.rhce.cc/pub/updates &（SSH到考试机时一定要 –X）
    rpm -ivh kernel-3.10.0-229.el7.x86_64.rpm

----------------------------------------------------------------------


九、系统host.rhce.cc提供了一个LDAP验证服务，你的系统按照下面要求绑定到这个服务：
验证服务的基准DN是dc=rhce,dc=cc
LDAP用于提供账号信息和验证信息连接应该使用位于http://host.rhce.cc/pub/domain11.crt的证书加密。 当正确配置后，ldapuser11可以登录你的系统，但是没有家目录，直到你完成autofs题目ldapuser11的密码是redhat

    安装：yum install authconfig-gtk.x86_64
    打开图形化界面：authconfig-gtk &
    图形化界面选择LDAP， 安装提示软件包
    设置DN、服务器、下载CA证书（复制粘贴）
    （dc=rhce,dc=cc)   (host.rhce.cc)        (http://host.rhce.cc/pub/domain11.crt)
    用id ldapuser11验证

----------------------------------------------------------------------


十、配置你的系统使它是rhgls.rhce.cc是一个NTP客户


    yum install system-config-date –y
    system-config-date & 图形化界面设置删除原有server   添加rhgls.rhce.cc 点击确定


------------------------------------------------------------


十一、配置autofs自动挂载LDAP用户的家目录，如下要求：
host.rhce.cc(192.168.122.10)使用NFS共享了/home/guest给你的系统，这个文件系统包含了预先设置好的用户ldapuser11的家目录
ldapuser11的家目录是在host.rhce.cc:/home/guests/ldapuser11
ldapuser11的家目录应该自动挂载到本地/home/guest下面的/home/guests/ldapuser11
家目录必须对用户具有可写权限 ldapuser11的密码是'redhat'

    yum install autofs –y
    vim /etc/auto.master中添加/home/guests /etc/auto.aa
    cp /etc/auto.misc /etc/auto.aa
    vim /etc/auto.aa中添加：
    ldapuser11 -fstype=nfs,rw,vers=3 host.rhce.cc: /home/guests/ldapuser11
    重启服务：systemctl restart autofs 
    设置开机自动启动：systemctl enable autofs
    验证题目：su – ldapuser11

--------------------------------------------------------------


十二、创建一个用户alex， uid为3400 。这个用户的密码为redhat

    useradd –u 3400 alex
    echo redhat | passwd –-stdin alex

---------------------------------------------------------------


十三、为你的系统上额外添加一个大小为512M的交换分区，这个交换分区在系统启动的时候应该自动挂载。不要移除和更改你系统上现存的交换分区

    查看分区情况：lsblk
    fdisk /dev/sda
    创建一个主分区，创建扩展分区，创建交换分区(mkswap /dev/sda swapon –s /dev/sda)
    刷新分区表：partprobe
    设置自动挂载：vim /etc/fstab /dev/vda5 swap swap defaults 0 0 
    查看分区：swapon –a swapon –s


----------------------------------------------------------------------------

十四、找出所有所有者是ira的文件，并把它们拷贝到/root/findresults目录

    创建目录：mkdir /root/findresults
    查询+拷贝：find / -user ira –exec cp –a {} /root/findresults/ \;


--------------------------------------------------------------------------------------

十五、在/usr/share/dict/words中找出所有包含seismic的行。复制这些行并按照原来顺序放在文件/root/lines中。/root/lines应该没有空白行，所有的行必须是/usr/share/dict/words中原行的精确复制。

    grep seismic /usr/share/dict/words > /root/lines
    cat /root/lines


----------------------------------------------------------------------------


十六、创建名为/root/backup.tar.bz2的备份文件，其中包含/usr/local的内容，tar必须使用bz2压缩

    cd /usr/local
    tar jcvf /root/backup.tar.bz2 * (如报错没有bzip2，则yum安装bzip2)

-----------------------------------------------------------------------------


十七、按照下面的要求创建一个新的逻辑卷：
逻辑卷命名为database，属于卷组datastore，且大小为50个扩展，在卷组datastore的逻辑卷每个扩展的大小为16MB，使用ext3格式化这个新的逻辑卷，此逻辑卷在系统启动的时候应该能自动挂载到/mnt/database

    lsblk
    fdisk /dev/vda
    创建扩展分区，改成逻辑分区(8e)
    pvscan 
    pvcreate /dev/vda6
    vgcreate –s 16 datastore /dev/vda6
    lvcreate -l 50 –n database datastore
    mkfs.vfat /dev/datastore/database（afat格式化）
    mkfs.ext4 /dev/datastore/database（ext4格式化）
    mkfs.xfs /dev/datastore/database( xfs格式化)
    创建目录：/mnt/database 
    vim /etc/fstab /dev/datastore/database /mnt/database vfat(格式根据考试要求) defaults 0 0
    检查挂载：mount –a df-hT

 