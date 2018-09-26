---
layout: post
title: "Jekyll 搭建个人博客"
img: 20180411-5.jpg 
date: 2018-04-11
description: Jekyll 搭建个人博客 
tag: [Blog, 技术分享]
---
## 前言
>  分享是程序员的美德

## 起由
写博客的初衷是记录工作中遇到的问题和解决方案，同时满足自己装逼的刚需，txtx。为了让自己更有归属感，起初是想完全自己完成前后端的搭建，但是理想的丰满总是被现实骨感打败，工作时间的不允许，加之对于一个喜欢使用各种新奇轮子的程序员，轻便，逼格，可玩性高是必备的三要素。遍览市面上的技术博客均无法满足装逼的要求，这个念头一直搁置了下来。

要不说 念念不忘，必有回响 呢。感谢[@蹉跎][1]同学的安利，我发现了*hexo*和*jekyll*这类博客框架，其轻便程度和theme风格都能让人隔着屏幕嗅到大（zhuang）神（bi）的气息。随即，惊喜之余用颤抖的双手在几小时搭建好了第一个个人博客。
这篇先讲讲如何搭建属（wan）于（quan）自（mian）己（fei）的Jekyll个人博客（当然hexo也很好，等有空再介绍，大同小异。）

## 搭建jekyll前需要了解的一些知识
------
>  “什么都略懂一点，生活更多彩一点。”  ——诸葛亮

### ruby与ruby gem
 - Ruby，一种简单快捷的面向对象（面向对象程序设计）脚本语言
 - Ruby的其中一个“程序”叫rubygems，简称 gem(ruby 1.9.2及其以上就已经默认安装了ruby gem的，所以无需再次手动安装)

### 什么是jekyll
Jekyll是一个页面生成工具，只用 Markdown (或 Textile)、Liquid、HTML & CSS 就可以构建可部署的静态网站。
同时，作为全球最大的程序员同性交友网站。GitHub提供了一个便于搞基的功能：GitHub Pages 可以用于托管你项目中的静态网页。简单的说，就是由jekyll将我们Github项目中的代码生成静态页面，由Github Pages托管并运行起来。
左手jekyll右手Github Pages，左手右手一个慢动作就可以告别主机服务商搭建我们自己的个人博客了。

### ruby与jekyll
jekyll是基于ruby的，所以搭建jekyll之前必须确保ruby正常安装 注意，**ruby版本需要高于2.0.0**

### jekyll与python
jekyll3.0之前，有一个语法高亮插件：Pygments，它基于python的，所以才会有各种教程里面都说搭建jekyll之前需要python环境。

但是,请注意 jekyll 3.0以后，语法高亮插件已经默认改成了 “rouge” 而它是基于ruby的，也就是说现在搭建jekyll,我们完全不必要再安装python环境 这样可以减少很大一部分工作量。

### 为什么需要切换gem源
切换gem源的方法在后面会提到，至于为什么。。。大清自有国情在。。
使用默认的https://rubygems.org/源可能会导致无法访问或各种找不到对应版本的包之类的错误，很多教程都告诉你需要切换成淘宝源，但是新的淘宝源已经停止维护了 **这是一个坑请绕过！**，我们需要切换的是ruby china源，新的gems源由腾讯云赞助
> http://gems.ruby-china.org

###如何解决其他问题
在你确保ruby版本和gems源都正确无误时，剩下的问题基本上都可以由错误提示通过相应的命令解决，最常见的就是。。gem install。。祝你好运。

对了，需要注意的是，如果你和我一样是在Windows下配置，还需要确保DEVELOPMENT KIT正确配置，后文会提到。

## 搭建jekyll的基本步骤
不论你是在linux还是windows下，都必须满足以下步骤：
 1. 正确安装ruby（windows下还需要安装DevKit）
 2. 切换ruby源
 3. 通过gem安装jekyll
 4. github上fork别人的模板或直接在http://jekyllthemes.org/中下载

## windows下搭建jekyll
话痨了这么久，现在开始进入正题！
说实话，在调查了这么多大神的教程，一直的印象就是windows下搭建要远比在linux下麻烦或经常出问题，但是两边实践过后发现其实在windows下搭建并没有想象中复杂，所以说实践出真知。

### 1 搭建本地环境
-----------
首先需要安装[ruby][2]来使用本地jekyll，很多教程中提示版本不要超过2.3，其实没有必要，在官方更新的2.4版本的installer已经集成了[ruby和相应版本的Devkit][3],我使用的是Ruby+Devkit 2.4.1-1(x64)
ruby安装完成后（记得添加入环境变量）需要安装[RubyGem][4]，gem是一个ruby的包管理系统，可以用gem很方便的在本地安装ruby应用，类似于python中的pip。下载安装包后解压到你本地任意位置。   

安装方法，在命令行下  
<code>cd {你解压的目录}</code>  
<code>ruby setup.rb</code>   
安装完成后命令行下输入<code>gem source -l</code> 查看当前添加gem源，  
然后通过  
<code>gem sources -r https://rubygems.org/</code> 和 <code>gem sources -r https://ruby.taobao.org/</code>    
移除官方源和淘宝源（最新的版本已经不默认添加淘宝源了），  
再使用命令  
<code>gem sources -a http://gems.ruby-china.org</code>添加ruby china源。   
注意这里使用的是http链接，如果使用https链接会遇到https请求失败的问题，需要[下载证书][5],  
指定环境变量  
<code>set SSL_CERT_FILE={你自己安装的目录}\cacert.pem</code>
完成后安装jekyll：  
<code>gem install jekyll</code>  
<code>gem install jekyll-paginate</code>    
安装完成后<code>jekyll -v</code>查看版本信息，正确显示就恭喜本地环境配置完成。   
本地调试可以通过<code>jekyll serve</code>命令在4000端口进行调试。
当选用不同的jekyll主题时，可能会要求安装其他jekyll包，同样通过<code>gem jekyll-XXX</code>安装即可。

## 2 创建Github仓库
--------
首先你需要有github账户  
<div align="center">
    <img  alt="你这不是废话嘛" src="/assets/img/in_post/2018-04-11-build-Jekyll-blog-1.png"/>
</div>   
要启用Github Pages，首先需要创建一个repo，名称为{你的用户名}.github.io，在以前的版本中需要在后台settings中手动开启Github Pages功能，现在系统检测到{你的用户名}.github.io会自动开启。   
如果你创建的仓库为空时（比如不添加README），会提示无法启用，这个不着急，等fork模板或上传你下载的theme后，repo中有了内容就会自动开启。  
<div align="center">
    <img  width="70%" height="70%" alt="Github Pages" src="/assets/img/in_post/2018-04-11-build-Jekyll-blog-2.png"/>
</div>   
后面的工作就是去找一下你喜欢的theme，fork下来，或者下载下来，把repo clone到本地，编辑器用什么都行，前端神器[WebStorm](https://www.jetbrains.com/webstorm/)或者[VS code](https://code.visualstudio.com/)都可以，都有VSC插件，颜值也都够。  

修改前端页面的时候需要了解一些前端知识和[Liquid](https://liquid.bootcss.com/)语法,熟悉前端的同学应该都能熟练运用，我就不班门弄斧了。  

大多数模板大同小异，主要以_config.yml文件控制页面的主要元数据。建议把encoding 设置成utf-8，这样有利于中英文双语的写作和阅读。
到这里你就可以放飞自我了！ 
<div align="center">
    <img  alt="装逼" src="/assets/img/in_post/2018-04-11-build-Jekyll-blog-3.png"/>
</div>   
所有的文章直接放在_posts文件夹下。
同时安利一下MarkDown编辑神器[作业部落](https://www.zybuluo.com)，真的很好用！

## 3 我还有很多新花样
------
jekyll玩法还是很多的，大家可以自己去探索，比如添加第三方评论，百度统计，相关文章等等，大家可以尽情探索。

第三方评论的话有点麻烦了，这个。。。大清自有国情在。我这里用到的畅言需要做备案，具体方法以后写给大家。   
我的博客要发表评论需要登录畅言，而畅言的登录做的实在。。。在网页端点击登录后如果看不到二维码请点击右下角的返回顶部按钮，你就可以找到二维码了，而移动端就爱莫能助了。

如果想使用自己的域名，可以在GoDaddy上购买，也（吃）不（点）贵（土）。DNS解析是交给DNSPod的，添加你的域名后回到GoDaddy上把域名的域名服务器修改为DNSPod提供的域名服务器  
<div align="center">
    <img  width="70%" height="70%" alt="修改DNS" src="/assets/img/in_post/2018-04-11-build-Jekyll-blog-4.png"/>
</div>  
然后回到DNSPod添加解析记录    
<div align="center">
    <img  width="70%" height="70%" alt="DNS解析1" src="/assets/img/in_post/2018-04-11-build-Jekyll-blog-5.png"/>
</div>   
然后添加DNS解析，主机记录填写为www，记录值填写你的{你的用户名}.github.io
<div align="center">
    <img  width="70%" height="70%" alt="DNS解析2" src="/assets/img/in_post/2018-04-11-build-Jekyll-blog-6.png"/>
</div>  
最后一步回到你的Github主页，在你的repo settings页面下修改Custom domin  
<div align="center">
    <img  width="70%" height="70%" alt="DNS解析3" src="/assets/img/in_post/2018-04-11-build-Jekyll-blog-7.png"/>
</div>  
之后就可以通过酷炫的域名访问你的博客了。High Five！  
> ###### *分享确实是一件很难的事情，坚持下去更加不容易，与君共勉吧!*

[1]: http://dawnzhang.me/
[2]: https://rubyinstaller.org/
[3]: https://rubyinstaller.org/downloads/
[4]: https://rubygems.org/pages/download
[5]: https://curl.haxx.se/ca/cacert.pem



