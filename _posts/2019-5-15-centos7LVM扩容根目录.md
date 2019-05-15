---
layout:     post
title:      Centos7LVM扩容
subtitle:   Centos7LVM扩容根目录
date:       2019-5-15
author:     MY
header-img: img/avatar_g.jpg
catalog: true
tags:
    - Centos7LVM扩容
    
---

#  前言：这是我在VMware Workstaion下实验的。



#  1.使用 fdisk -l 查看这块新盘（我新加为10G sdc）。
![](LVM/LVM1_LI.png)




#  2.对新盘分区并创建lvm分区格式。
![](LVM/LVM2_LI.png)



#  3.创建物理卷（PV），使用 pvcreate 命令创建物理卷，pvdisplay 查看物理卷信息。
![](LVM/LVM3_LI.png)



 
#  4.将PV加入卷组（VG）使用 vgextend 命令把/dev/vdb1加入到centos，使用 vgdisplay 查看卷组信息，显示卷组名为(vg name)centos，空闲大小(free)为0。
![](LVM/LVM4_LI.png)




#  5.逻辑卷扩容，使用 lvextend 命令进行逻辑卷扩容：lvextend -l +100%FREE /dev/centos/xxx
![](LVM/LVM5_LI.png)



#  6.使用 xfs_growfs 命令在线调整xfs格式文件系统大小（CentOS6使用resize2fs）：xfs_growfs /dev/centos/xxx
![](LVM/LVM6_LI.png)



