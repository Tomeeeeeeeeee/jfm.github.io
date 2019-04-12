---
layout:     post
title:      centos7
subtitle:   MariaDB数据库安装
date:       2019-4-11
author:     MY
header-img: img/post-bg-coffee.jpg
catalog: true
tags:
    - centos7
	- MariaDB数据库
    
---

>记录一下过程
#数据库常用命令
安装mysql数据库: yum install mariadb-server mariadb-libs mariadb   yum isntall mariadb*
启用数据库: systemctl start mariadb
关闭数据库: systemctl stop mariadb
重启数据库: systemctl restart mariadb
设置为开机启动: systemctl enable mariadb
查看数据库状态:  systemctl status mariadb
卸载数据库: yum -y remove mari*
删除数据库文件: rm -rf /var/lib/mysql/*

#systemctl是CentOS7的服务管理工具中主要的工具，它融合之前service和chkconfig的功能于一体。
 
启动一个服务：systemctl start firewalld.service
关闭一个服务：systemctl stop firewalld.service
重启一个服务：systemctl restart firewalld.service
显示一个服务的状态：systemctl status firewalld.service
在开机时启用一个服务：systemctl enable firewalld.service
在开机时禁用一个服务：systemctl disable firewalld.service
查看服务是否开机启动：systemctl is-enabled firewalld.service
查看已启动的服务列表：systemctl list-unit-files|grep enabled
查看启动失败的服务列表：systemctl --failed


#对 MariaDB 进行安全配置
>通过以下命令进行安全配置，根据实际情况用Y/N回复以下问题：设置 MariaDB 的 root 账户密码，删除匿名用户，禁用 root 远程登录，删除测试数据库，重新加载权限表。

mysql_secure_installation

本人全都是选择了Y，然后按回车。

>在配置完数据库的安全配置后，可以通过以下命令查看版本，确认 MariaDB已安装成功。

mysql --version

>可以通过 MariaDB 命令行登录，然后对数据库进行sql查询操作。

mysql -u root -p


#为 MariaDB 配置远程访问权限
在第一步中如果禁用 root 远程登录选择 Y 的话就不能在别的电脑通过navicat等工具连接到数据库，这时就需要给对应的 MariaDB 账户分配权限，允许使用该账户远程连接到MariaDB。可以输入以下命令查看账号信息：

select User, host from mysql.user;

root账户中的host项是localhost表示该账号只能进行本地登录，我们需要修改权限，输入命令：

GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'password' WITH GRANT OPTION;

修改权限。%表示针对所有IP，password表示将用这个密码登录root用户，如果想只让某个IP段的主机连接，可以修改为：

GRANT ALL PRIVILEGES ON *.* TO 'root'@'192.168.71.%' IDENTIFIED BY 'my-new-password' WITH GRANT OPTION;

最后别忘了：

FLUSH PRIVILEGES;

保存更改后，再看看用户账号信息：

这个时候发现相比之前多了一项，它的host项是%，这个时候说明配置成功了，我们可以用该账号进行远程访问了。

#CentOS 7 开放防火墙端口
在上一步后如果还是不能远程连上数据库的话应该就是3306端口被防火墙拦截了，这时我们就需要关闭防火墙或者开放防火墙端口。

>关闭防火墙：

systemctl stop firewalld.service            #停止firewall

systemctl disable firewalld.service        #禁止firewall开机启动

>开放防火墙端口，开启后要重启防火墙：

firewall-cmd --zone=public --add-port=3306/tcp --permanent

firewall-cmd --reload

>查看80端口是否打开：

firewall-cmd --query-port=80/tcp

>开启端口：
 
　　firewall-cmd --zone=public --add-port=80/tcp --permanent
 
>命令含义：
 
　　--zone #作用域
 
　　--add-port=80/tcp  #添加端口，格式为：端口/通讯协议
　　 
　　--permanent   #永久生效，没有此参数重启后失效
 
>重启防火墙
 
　　firewall-cmd --reload

#firewalld的基本使用
 
启动： systemctl start firewalld
查看状态： systemctl status firewalld
停止： systemctl disable firewalld
禁用： systemctl stop firewalld
查看版本： firewall-cmd --version
查看帮助： firewall-cmd --help
显示状态： firewall-cmd --state
查看所有打开的端口： firewall-cmd --zone=public --list-ports
更新防火墙规则： firewall-cmd --reload
查看区域信息:  firewall-cmd --get-active-zones
查看指定接口所属区域： firewall-cmd --get-zone-of-interface=eth0
拒绝所有包：firewall-cmd --panic-on
取消拒绝状态： firewall-cmd --panic-off
查看是否拒绝： firewall-cmd --query-panic

#开启一个端口
 
>添加
firewall-cmd --zone=public --add-port=80/tcp --permanent    （--permanent永久生效，没有此参数重启后失效）
>重新载入
firewall-cmd --reload
>查看
firewall-cmd --zone= public --query-port=80/tcp


#设置数据库字母大小写不敏感
vi /etc/my.cnf.d/server.cnf

>在[mysqld]下加上】

lower_case_table_names=1

默认是等于0的,即大小写敏感。改成1就OK了。如果之前已经建了数据库要把之前建立的数据库删除，重建才生效。


#设置MariaDB数据库默认编码
MariaDB的默认编码是latin1，插入中文会乱码，因此需要将编码改为utf8。

#1.登录，使用以下命令查看当前使用的字符集，应该有好几个不是utf8格式。

SHOW VARIABLES LIKE 'character%';

#2.修改的配置文件

vi /etc/my.cnf.d/client.cnf

>在[client]字段里加入

default-character-set=utf8

vi /etc/my.cnf.d/server.cnf

>在[mysqld]字段里加入

character-set-server=utf8
#3.重启 MariaDB 配置生效。

systemctl restart mariadb




