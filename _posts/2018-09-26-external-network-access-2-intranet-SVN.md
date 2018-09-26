---
layout: post
title: "通过NAT搭建外网可访问的SVN"
img: 20180926.jpg 
date: 2018-09-26
description: 通过NAT搭建外网可访问的SVN 
tag: [Blog, 技术分享,工作日志]
---

## 起由
这篇文章主要来源于工作中的一个小需求，并不算严格意义上的技术分享（毕竟。。。有点太简单了），主要目的是记录一下工作中可能还会遇到的问题的解决方案。

起由是参与的项目的人员都在现场进行项目部署和调试，同时将来人员拉回来之后需要在实验室继续研发，所以需要在“家”搭建一套开发环境和仿真环境。希望能够搭建一个能在外网访问的SVN服务器。

由于之前有利用NAT地址转换实现外网访问公司内部服务的经验，于是很自然想到同样使用地址转化来完成。

>  注意：本篇文章可能会很短。。很短。。

### 1 安装Visual SVN Server
-----------
首先需要安装[Visual SVN Server][1]。具体的安装步骤在此不表。
<div align="center">
    <img  alt="捂脸" src="/assets/img/in_post/2018-09-26-external-network-access-2-intranet-SVN-1.jpeg"/>
</div>  
感觉工作的一半都跳过了。。。

### 2 打开网关管理后台，添加地址
--------
确认公司内网使用的网关厂家。我们使用的迈普的网关，所以打开后台管理页面。   
登入管理员账号，找到资源管理标签页下的地址管理
<div align="center">
    <img  width="70%" height="70%" alt="地址管理1" src="/assets/img/in_post/2018-09-26-external-network-access-2-intranet-SVN-2.jpeg"/>
</div>   
 点击新建，进入新建地址页面，输入地址对象的名称（便于在配置NAT的时候找到配置的地址），地址项目选择子网地址，IP输入放置你的SVN Server的主机IP，将其添加致列表
<div align="center">
    <img  width="70%" height="70%" alt="地址管理2" src="/assets/img/in_post/2018-09-26-external-network-access-2-intranet-SVN-3.jpeg"/>
</div>  
提交！

### 3 添加自定义服务
------
地址添加完后需要添加我们的自定义服务
<div align="center">
    <img  width="70%" height="70%" alt="自定义服务1" src="/assets/img/in_post/2018-09-26-external-network-access-2-intranet-SVN-4.jpeg"/>
</div>  
切换到自定义服务，新建，类型选择TCP，将目的端口设置为需要暴露在外网的IP端口，源端口设置为0-65535（即为不约束源端口）
<div align="center">
    <img  width="70%" height="70%" alt="自定义服务2" src="/assets/img/in_post/2018-09-26-external-network-access-2-intranet-SVN-5.jpeg"/>
</div>  
提交！ 

### 4 设置NAT
------
下一步进入网络管理对NAT进行设置。
在这一步之前，需要确定已经设置好了对外的物理接口，也就是希望外网访问时要连接的IP入口。 

之后需要先将SVN服务器IP添加进地址池。
<div align="center">
    <img  width="70%" height="70%" alt="NAT1" src="/assets/img/in_post/2018-09-26-external-network-access-2-intranet-SVN-6.jpeg"/>
</div> 
这一步很简单，把SVN服务器IP填进去就可以，因为我们只需要对单机地址进行转换，所以地址开始和技术设置成一样就可以。
然后切换到目的NAT，新建--注意，源地址是网关中设置的所有不属于内网的IP集合，也就是我这里的“外网”，目的地址为公司对外的IP，接口选择上文中提到的物理接口名称，转换类型选择端口映射，转换后的端口就是在服务器上SVN Server监听的端口，我这里监听的是8443，所以设置成8443.
<div align="center">
    <img  width="70%" height="70%" alt="NAT1" src="/assets/img/in_post/2018-09-26-external-network-access-2-intranet-SVN-7.jpeg"/>
</div> 

切换到自定义服务，新建，类型选择TCP，将目的端口设置为需要暴露在外网的IP端口，源端口设置为0-65535（即为不约束源端口）
<div align="center">
    <img  width="70%" height="70%" alt="NAT3" src="/assets/img/in_post/2018-09-26-external-network-access-2-intranet-SVN-5.jpeg"/>
</div>  
提交！
记得保存配置，大功告成！
现在在现场的同事就可以访问到公司内网的SVN提交和维护代码了，是不是很简单~
High Five!
<div align="center">
    <img alt="装逼王" src="/assets/img/in_post/2018-09-26-external-network-access-2-intranet-SVN-8.jpeg"/>
</div>  
> ##### *虽然内容很简单，但是能够记录工作中的点点滴滴也是极好的!*

[1]: https://www.visualsvn.com/server/




