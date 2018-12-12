---
layout:      post
title:       "JS IP查询接口"
subtitle:    "IP地址与归属地查询"
date:        2018-10-10
author:      "Boy's Zhang"
header-img:  "img/post-bg-js-interface.jpg"
tags:
  - JS
  - interface
---


> 由于想在本站底部加一个显示地理位置的功能，故统计了一些js的IP接口（不断更新中）。
> 最近一次更新：2018-12-12

IP地址查询API接口
=====================

新浪的IP地址查询接口：http://int.dpool.sina.com.cn/ （测试，已挂）

搜狐IP地址查询接口（默认GBK）：http://pv.sohu.com/cityjson
```json
var returnCitySN = 
{
    "cip": "114.114.114.114", 
    "cid": "CN", 
    "cname": "CHINA"
}
```
搜狐IP地址查询接口（可设置编码）：http://pv.sohu.com/cityjson?ie=utf-8
```json
var returnCitySN = 
{
    "cip": "114.114.114.114", 
    "cid": "CN", 
    "cname": "CHINA"
}
```
搜狐另外的IP地址查询接口：http://txt.go.sohu.com/ip/soip 
```
String.prototype.getQueryString=function(v){var reg=new RegExp("(^|&|\\?)" + v + "=([^&]*)(&|$)"), r;if(r=this.match(reg)){return unescape(r[2]);}return null;};var sohu_IP_Loc="unknown",LocUrl=document.location.href;if((LocUrl.indexOf("sohusce.com") >= 0)||(LocUrl.indexOf("sohu.com") >= 0)||(LocUrl.indexOf("chinaren.com") >= 0)||(LocUrl.indexOf("17173.com") >= 0)||(LocUrl.indexOf("focus.cn") >= 0)){window.sohu_user_ip="114.114.114.114";sohu_IP_Loc="CN310000";sohu_IP_Loc_V="CN";}var AdLoc2=sohu_IP_Loc.substr(0,2),AdLoc4=sohu_IP_Loc.substr(0,4),AdLoc6=sohu_IP_Loc.substr(0,6);if(window.location.href.getQueryString("ip"))sohu_IP_Loc=AdLoc2=AdLoc4=AdLoc6=window.location.href.getQueryString("ip");
```
IP  API查询接口：http://ip-api.com/json/　　# 国际化英文显示

http://ip-api.com/json/114.114.114.114?lang=zh-CN      #中文使用

```json
{
    "as": "AS174 Cogent Communications", 
    "city": "Beijing", 
    "country": "China", 
    "countryCode": "CN", 
    "isp": "China Unicom Shandong Province network", 
    "lat": 39.9042, 
    "lon": 116.407, 
    "org": "NanJing XinFeng Information Technologies, Inc.", 
    "query": "114.114.114.114", 
    "region": "BJ", 
    "regionName": "Beijing", 
    "status": "success", 
    "timezone": "Asia/Shanghai", 
    "zip": ""
}
```
烧饼博客查询接口：https://api.ip.sb/jsonip
```json
{
    "ip": "114.114.114.114"
}
```


查询IP归属地的接口
=====================

新浪的IP地址查询接口：http://int.dpool.sina.com.cn/iplookup/iplookup.php?format=js   （经测试，已挂）

新浪多地域测试方法：http://int.dpool.sina.com.cn/iplookup/iplookup.php?format=js&ip=114.114.114.114  （经测试，已挂）



淘宝的IP地址查询接口：http://ip.taobao.com/service/getIpInfo.php?ip=114.114.114.114
```json
{
    "code": 0, 
    "data": {
        "ip": "114.114.114.114", 
        "country": "中国", 
        "area": "", 
        "region": "江苏", 
        "city": "南京", 
        "county": "XX", 
        "isp": "XX", 
        "country_id": "CN", 
        "area_id": "", 
        "region_id": "320000", 
        "city_id": "320100", 
        "county_id": "xx", 
        "isp_id": "xx"
    }
}
```

聚合（普通用户被限制次数）的IP地址查询接口：http://apis.juhe.cn/ip/ip2addr?ip=114.114.114.114&dtype=&key=[yourKey]    (没有申请key所以不贴结果了)



太平洋IP地址库API接口：http://whois.pconline.com.cn/ipJson.jsp?ip=114.114.114.114&json=true
```json
{
    "ip": "114.114.114.114", 
    "pro": "江苏省", 
    "proCode": "320000", 
    "city": "南京市", 
    "cityCode": "320100", 
    "region": "", 
    "regionCode": "0", 
    "addr": "江苏省南京市 电信", 
    "regionNames": "", 
    "err": ""
}
```
太平洋IP查询的更多接入方式：http://whois.pconline.com.cn/


百度IP查询接口：http://api.map.baidu.com/location/ip?ak=你的密钥&ip=114.114.114.114   (没有申请key所以不贴结果了)

```json
{
    "address":"CN|江苏|徐州|None|CHINANET|0|0",
    "content":{
        "address_detail":{
            "province":"江苏省",
            "city":"徐州市",
            "district":"",
            "street":"",
            "street_number":"",
            "city_code":316
        },
        "address":"江苏省徐州市",
        "point":{
            "y":"4041039.6",
            "x":"13045462.3"
        }
    },
    "status":0
}
```

IPIP查询接口： http://freeapi.ipip.net/118.28.8.8 免费接口（限速每天1000次，且只提供地理位置）
```json
[
    "中国", 
    "天津", 
    "天津", 
    "", 
    "鹏博士"
]
```



IP  API查询中文接口：http://ip-api.com/json/114.114.114.114?lang=zh-CN　　# 中文显示
```json
{
    "as": "AS174 Cogent Communications", 
    "city": "北京", 
    "country": "中国", 
    "countryCode": "CN", 
    "isp": "China Unicom Shandong Province network", 
    "lat": 39.9042, 
    "lon": 116.407, 
    "org": "NanJing XinFeng Information Technologies, Inc.", 
    "query": "114.114.114.114", 
    "region": "BJ", 
    "regionName": "北京", 
    "status": "success", 
    "timezone": "Asia/Shanghai", 
    "zip": ""
}

```

IP  API查询接口：http://ip-api.com/json/114.114.114.114?lang=zh-CN　　# 查询某个ip的信息
```json
{
    "as": "AS174 Cogent Communications", 
    "city": "Beijing", 
    "country": "China", 
    "countryCode": "CN", 
    "isp": "China Unicom Shandong Province network", 
    "lat": 39.9042, 
    "lon": 116.407, 
    "org": "NanJing XinFeng Information Technologies, Inc.", 
    "query": "114.114.114.114", 
    "region": "BJ", 
    "regionName": "Beijing", 
    "status": "success", 
    "timezone": "Asia/Shanghai", 
    "zip": ""
}
```
烧饼博客查询接口：https://api.ip.sb/geoip/114.114.114.114
```json
{
    "longitude": 113.7266, 
    "latitude": 34.7725, 
    "area_code": "0", 
    "dma_code": "0", 
    "organization": "AS174 Cogent Communications", 
    "country": "China", 
    "ip": "114.114.114.114", 
    "country_code3": "CHN", 
    "continent_code": "AS", 
    "country_code": "CN"
}
```


