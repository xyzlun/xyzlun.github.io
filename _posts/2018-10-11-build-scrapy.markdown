---
layout: post
title: "技术化装修 装友会论坛爬虫"
img: malaysia.jpg
date: 2018-10-11
description: 技术化装修 装友会论坛爬虫 
tag: [Blog, 技术分享,工作日志]
---

## 前言
> 最近在装修自己的房子。是的，我膨胀了，终于要在成都安下自己的家了~一直以来选择装修公司、找第三方监理和主材选择，以及各种装修经验都是泡在第四城的装友会来学习的。某天和老婆在讨论浴室柜选哪家的时候，突然想知道一下装友们都是怎么选的，结果发现论坛并没有内容搜索，就是这么old school~作为一个菜鸟pythoner，自然想到爬一爬帖子咯，也是刚好闲的没事（可能自己是个假程序员）。

## 1 搭建开发环境
肯定是先virtualenv啦，
<code>D:\My_Project>virtualenv -p D:\Python3\python3.exe 4cScrapy</code> 
然后activate命令激活虚拟环境。
首先就是一波pip install~
现在scrapy中已经包含了zope.interface、pyOpenSSL、twisted、lxml

之后就是：<code>scrapy startproject 4C</code> 出发了小瓜皮们！
<div align="center">
    <img  alt="error" src="/assets/img/in_post/2018-10-11-build-scrapy-1.png"/>
</div>
没想到一开始就翻车了。。。好吧，改个名字，重新出发！

IDE的选择有很多，我是习惯用Pycharm了，所以用Pycharm打开工程目录，我们可以得到下图这样一个目录结构
<div align="center">
    <img  alt="dir" src="/assets/img/in_post/2018-10-11-build-scrapy-2.png"/>
</div>
 Pycharm是没有办法直接调试scrapy的，所以需要我们自己做一些配置。首先在spider目录下新建一个py文件，在这个阶段我们需要在这个文件中写一些基本的逻辑。
<div align="center">
    <img  width="70%" height="70%" alt="spider" src="/assets/img/in_post/2018-10-11-build-scrapy-3.png"/>
</div>
其中start_request是我们需要爬取的url。进行多页爬行需要返回一个列表，或者使用生成器。我这里暂时爬论坛的前两页。parse()方法将调用一个方法处理每一个请求所下载的响应，我们首先把他们打印出来，后面再进行复杂的操作。

之后在上一级目录下新建一个main函数文件，名字选一个心仪的，毕竟要作为调试的入口。内容如下
<div align="center">
    <img  width="70%" height="70%" alt="main" src="/assets/img/in_post/2018-10-11-build-scrapy-4.png"/>
</div>
注意这里cmdline.execute方法中的参数字符串最后一个name是与上一步中建的类的name属性保持一致的。
以后调试项目直接运行main.py就可以。

当然也可以在Pycharm中配置调试选项
<div align="center">
    <img  alt="debug" src="/assets/img/in_post/2018-10-11-build-scrapy-5.png"/>
</div>
把mian.py添加进来，以后就小绿三角走起了~

如果遇到报错ModuleNotFoundError: No module named 'win32api'，那就安装win32模块就好了。直接pip安装不上的可以来下载对应版本的[pywin32][1]。

## 2 初识Middleware
------
相信大家猜到了，这个项目一开始肯定是跑不起来的。运行后提示报错
<div align="center">
    <img  width="70%" height="70%" alt="error_info" src="/assets/img/in_post/2018-10-11-build-scrapy-6.png"/>
</div>
416状态码的官方解释是：416 Requested Range Not Satisfiable 服务器不能满足客户在请求中指定的Range头。
理解下来是我们的爬虫向服务器发送的request中的header不满足要求，服务器不认为它是一个浏览器。因此解决方案就有两个：

1. 在request上戴上浏览器的header；
2. 使用代理。
我们选择使用Middleware中间件，通过Middleware我们可以对请求信息作出一些修改，比如常用的设置UA、代理、登录信息等等都可以通过Middleware来配置。

```python
DOWNLOADER_MIDDLEWARES = {
   'scrapy4C.middlewares.Scrapy4CDownloaderMiddleware': 543,
    "scrapy4C.middlewares.UserAgentMiddleware": 401,
}
```

首先在setting.py中注册中间件的名称，然后在项目下middlewares.py中添加代码。咱们爬的论坛是会检查UA的，那我们就带上好咯


import random
agents = [
"Mozilla/5.0 (Windows; U; Windows NT 6.1; en-US) AppleWebKit/532.5 (KHTML, like Gecko) Chrome/4.0.249.0 Safari/532.5",
"Mozilla/5.0 (Windows; U; Windows NT 5.2; en-US) AppleWebKit/532.9 (KHTML, like Gecko) Chrome/5.0.310.0 Safari/532.9",
"Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US) AppleWebKit/534.7 (KHTML, like Gecko) Chrome/7.0.514.0 Safari/534.7",
"Mozilla/5.0 (Windows; U; Windows NT 6.0; en-US) AppleWebKit/534.14 (KHTML, like Gecko) Chrome/9.0.601.0 Safari/534.14",
"Mozilla/5.0 (Windows; U; Windows NT 6.1; en-US) AppleWebKit/534.14 (KHTML, like Gecko) Chrome/10.0.601.0 Safari/534.14",
"Mozilla/5.0 (Windows; U; Windows NT 6.1; en-US) AppleWebKit/534.20 (KHTML, like Gecko) Chrome/11.0.672.2 Safari/534.20",
"Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/534.27 (KHTML, like Gecko) Chrome/12.0.712.0 Safari/534.27",
"Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/535.1 (KHTML, like Gecko) Chrome/13.0.782.24 Safari/535.1",
]

class UserAgentMiddleware(object):
    def process_request(self, request, spider):
    agent = random.choice(agents)
    request.headers["User-Agent"] = agent


> ###### *分享确实是一件很难的事情，坚持下去更加不容易，与君共勉吧!*

[1]: https://www.lfd.uci.edu/~gohlke/pythonlibs/