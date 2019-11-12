---
layout:      post
title:       "上海电信E8-C光猫网关密码获取"
subtitle:    "----telecomadmin 密码 "
date:        2019-11-5
author:      "Boy's Zhang"
header-img:  "img/post-bg-security.jpg"
tags:
  - 软路由
  - 路由器
---

----------------------------------------------------------

> 由于搬家需要新办理网络，所以申请了电信宽带（主要是联通小区没有接入，移动网络太渣），给了一个光猫HG220G，在网上找了不少破解方法都没有成功，后来偶然间发现了一篇文章，发现该机器还有工程模式。



----------------------------------------------------------------



在浏览器上输入 http://192.168.1.1/logoffaccount.html  设置隐藏用户改为启用，这样就可以用工程账号有登录了。

![HideUsers](/img/in-post/2019-11-5-GetPasswordTelecom/HideUsers.png)

登录工程帐号（用户名：fiberhomehg2x0密码：hg2x0），登录网址http://192.168.1.1/（如果工程账号登录不了，还是最好联系运营商。）

启用Telnet服务，设置用户名和密码。

![EngineeringMode](/img/in-post/2019-11-5-GetPasswordTelecom/EngineeringMode.png)

用telnet登录光猫，用get telpwd可以获取你要的密码，即便局端修改了也会跟着变。

登陆账号与密码可通过上一步骤进行修改，我这里为admin/admin


![GetPassword](/img/in-post/2019-11-5-GetPasswordTelecom/GetPassword.png)
