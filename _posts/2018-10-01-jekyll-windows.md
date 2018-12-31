---
layout:      post
title:       "jekyll本地环境搭建"
subtitle:    "----windows下安装记录"
date:        2018-10-01
author:      "Boy's Zhang"
header-img:  "img/post-bg-jekyll.jpg"
catalog:     true
tags:
  - jekyll
---

> 前段时间使用jeklly搭建了一个博客，开始记录生活中点点滴滴

**使用jeklly搭建博客，每次调试都需要push到github来预览效果就很麻烦，所以在本地搭建调试环境是一个很不错的选择：**

我个人参考[windows下安装jekyll](https://www.jianshu.com/p/88e3474cef72)搭建的，后经试验发现官网已经将环境打包好，所以简化了很多步骤

- 安装ruby+devkit
- 安装jekyll

### 安装ruby+devkit
首先到官网下载exe，链接[地址](https://rubyinstaller.org/downloads/)

![download](/img/in-post/2018-10-01-jekyll-windows/download.png)

由于已经打包好了，根据自己的机型选择一个exe下载就好，我选择的是上一个版本2.4.5-1(x64)

跟着一路点下去安装就好，需要注意的就是要安装在根目录，还有要把环境变量也勾上

![setup-ruby-devkit](/img/in-post/2018-10-01-jekyll-windows/setup-ruby-devkit.png)

之后弹出来一个安装选项，选择3全部安装就好

![MSYS](/img/in-post/2018-10-01-jekyll-windows/MSYS.png)

安装好之后测试一下环境
```
ruby -v
gem -v
```
如果都有版本输出，就可以安装jekyll了

![ruby-gem-v](/img/in-post/2018-10-01-jekyll-windows/ruby-gem-v.png)


### 安装jekyll

gem install jekyll

安装好，测试一下

jekyll -v

![jekyll-v](/img/in-post/2018-10-01-jekyll-windows/jekyll-v.png)

之后就可以输入jekyll serve开启本地的环境了

![jekyll-serve](/img/in-post/2018-10-01-jekyll-windows/jekyll-serve.png)
### 遇到的问题

1、最后一步安装完成之后，出现错误
```
Dependency Error: Yikes! It looks like you don’t have jekyll-paginate or one of its dependencies installed. 
In order to use Jekyll as currently configured, you’ll need to install this gem. 
The full error message from Ruby is: ‘cannot load such file – jekyll-paginate’ If you run into trouble, you can find helpful resources at Getting Help
jekyll 3.1.2 | Error: jekyll-paginate
```

只需运行`gem install jekyll-paginate`安装相应的模块即可

2、警告
```
Configuration file: /_config.yml
            Source: .
       Destination: ./_site
      Generating...
                    done.
  Please add the following to your Gemfile to avoid polling for changes:
    gem 'wdm', '>= 0.1.0' if Gem.win_platform?


```

这个其实无所谓，运行`gem install wdm`，安装即可解决