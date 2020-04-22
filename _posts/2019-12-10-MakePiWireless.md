---
layout:      post
title:       "树莓派3B+变成WiFi热点"
subtitle:    "----hostapd udhcpd "
date:        2019-12-10
author:      "Boy's Zhang"
header-img:  "img/post-bg-MakePiWireless.jpg"
tags:
  - 软路由
  - 树莓派
---

> 由于家里换了软路由，没有无线网卡，正好有台闲置的树莓派，就拿来做无线上网ap，网上也有一键开启脚本，但是想自己折腾一下。

### 基础环境

+ 树莓派3B+
+ 官方的Raspbian Buster操作系统
+ hostapd： 该软件能使无线网卡工作在软AP(Access Point)模式，即无线路由器
+ udhcpd： 该软件能够同时提供DHCP和DNS服务

### 网卡配置

修改/etc/network/interfaces

```shell
# interfaces(5) file used by ifup(8) and ifdown(8)

# Please note that this file is written to be used with dhcpcd
# For static IP, consult /etc/dhcpcd.conf and 'man dhcpcd.conf'

# Include files from /etc/network/interfaces.d:
source-directory /etc/network/interfaces.d


auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 192.168.1.101
netmask 255.255.255.0
gateway 192.168.1.201

auto wlan0
#allow-hotplug wlan0
iface wlan0 inet static
address 192.168.10.1
netmask 255.255.255.0
```

其中eth0是有线网卡，wlan0为无线网卡，网卡ip可以自己定义，我这里路由器地址为192.168.1.201，无线段使用的192.168.10.0/24段

### 安装hostapd和udhcpd

如何设置国内源，我这里就不说了，可以查看我之前的文章。

```shell
sudo apt-get update

sudo apt-get install hostapd udhcpd

systemctl  restart hostapd

systemctl  restart udhcpd

systemctl  enable hostapd

systemctl  enable udhcpd
```

### 配置hostapd

```shell
vim  /etc/default/hostapd

#将#DAEMON_CONF=""修改为DAEMON_CONF="/etc/hostapd/hostapd.conf"
# Defaults for hostapd initscript
#
# See /usr/share/doc/hostapd/README.Debian for information about alternative
# methods of managing hostapd.
#
# Uncomment and set DAEMON_CONF to the absolute path of a hostapd configuration
# file and hostapd will be started during system boot. An example configuration
# file can be found at /usr/share/doc/hostapd/examples/hostapd.conf.gz
#
#DAEMON_CONF=""

DAEMON_CONF="/etc/hostapd/hostapd.conf"
# Additional daemon options to be appended to hostapd command:-
# 	-d   show more debug messages (-dd for even more)
# 	-K   include key data in debug messages
# 	-t   include timestamps in some debug messages
#
# Note that -B (daemon mode) and -P (pidfile) options are automatically
# configured by the init.d script and must not be added to DAEMON_OPTS.
#
#DAEMON_OPTS=""
```

再配置hostapd.conf

```shell
vim /etc/hostapd/hostapd.conf

interface=wlan0
ssid=pi
channel=7
macaddr_acl=0
auth_algs=1
ignore_broadcast_ssid=0
wpa=2
wpa_passphrase=playboyzhang
wpa_key_mgmt=WPA-PSK
rsn_pairwise=CCMP
ieee80211n=1
hw_mode=g
wmm_enabled=1
driver=nl80211
```

各项配置说明

```shell

#设置默认的接入点为无线网卡 wlan0
interface=wlan0

#设置驱动程序为 nl80211
driver=nl80211

#设置无线网络 SSID 为 Liushoujin's Raspberrypi
ssid=Liushoujin's Raspberrypi

#设置网卡的工作模式为 2.4GHz
hw_mode=g

#设置无线通道为6，如果发现连接速度慢或有干扰，也可以设置为其他数值 
channel=6

#使能 802.11n
ieee80211n=1

#使能 WMM
wmm_enabled=1

#使能具有20ns保护间隔的40MHz通道
ht_capab=[HT40][SHORT-GI-20][DSSS_CCK-40]

#接受所有MAC地址
macaddr_acl=0

#使用WPA身份验证
auth_algs=1

#要求客户端知道网络名称
ignore_broadcast_ssid=0

#使用WPA2
wpa=2

#使用预共享密钥
wpa_key_mgmt=WPA-PSK

#网络密码
wpa_passphrase=12345678

#使用AES代替TKIP
rsn_pairwise=CCMP
```

### 配置dhcp服务

vim /etc/udhcpd.conf

仅需修改以下几部分
```shell
# Sample udhcpd configuration file (/etc/udhcpd.conf)

# The start and end of the IP lease block

start		192.168.10.20	#default: 192.168.0.20
end		192.168.10.100	#default: 192.168.0.254


# The interface that udhcpd will use

interface	wlan0		#default: eth0
                '
                '
                '
                '
                '
#Examles
opt	dns	192.168.1.201
option	subnet	255.255.255.0
opt	router	192.168.10.1
opt	wins	192.168.1.201
#option	dns	129.219.13.81	# appened to above DNS servers for a total of 3
option	domain	local
option	lease	864000		# 10 days of seconds
                '
                '
                '
                '
# Static leases map
#static_lease 00:60:08:11:CE:4E 192.168.0.54
#static_lease 00:60:08:11:CE:3E 192.168.0.44

```
修改无线网卡的dhcp分配ip地址段，dns 网关等信息

在修改/etc/default/udhcpd
```shell
# Comment the following line to enable
#DHCPD_ENABLED="no"

# Options to pass to busybox' udhcpd.
#
# -S    Log to syslog
# -f    run in foreground

DHCPD_OPTS="-S"
```
将DHCPD_ENABLED="no"添加注释

### 通过iptables配置路由转发

首先打开配置文件/etc/sysctl.conf，去掉net.ipv4.ip_forward=1前面的注释符。
执行命令sysctl -p使配置文件生效。

再执行如下命令配置防火墙规则：

```shell
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE

iptables -A FORWARD -i eth0 -o wlan0 -m state --state RELATED,ESTABLISHED -j ACCEPT

iptables -A FORWARD -i wlan0 -o eth0 -j ACCEPT

# 永久保存防火墙策略
# 创建文件
sudo sh -c "iptables-save > /etc/iptables.ipv4.nat"
# 修改系统，添加启动任务：
pi@raspberrypi:~ $ sudo vim /etc/rc.local
sh -c "echo 1 > /proc/sys/net/ipv4/ip_forward"
iptables-restore < /etc/iptables.ipv4.nat

exit 0
```
### 重启服务

```shell
systemctl  restart hostapd

systemctl  restart udhcpd
```

### 配置过程报错总结

+ 配置文件名称拼错字母
+ 配置中存在中文字符
+ 错误基本都在日志中，细心查看日志都可以配置成功
+ Failed to restart hostapd.service: Unit hostapd.service is masked.
  解决方法：
          systemctl umask hostapd



