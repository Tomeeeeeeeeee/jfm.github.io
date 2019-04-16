---
layout:     post
title:      Centos 7
subtitle:   安装Apache
date:       2019-4-12
author:     MY
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - Centos 7  Apache
    
---

>随记


安装httpd:yum install -y httpd


启动Apache:systemctl start httpd.service


设置开机启动:systemctl enable httpd.service


网页文件放置位置:/var/www/html


取消掉Apache欢迎界面:gedit /etc/httpd/conf.d/welcome.conf 注释掉7-10行


网页显示乱码:gedit /etc/httpd/conf/httpd.conf   把AddDefaultCharset UTF-8中UTF-8改为GB2312

