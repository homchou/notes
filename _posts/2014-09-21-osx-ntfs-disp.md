---
layout: post
title:  "解决下OSX下NTFS硬盘上文件突然消失的问题"
date:   2014-9-21 18:43:00
categories: osx
---
#开始 
由于Window和MAC OSX的默认的文件系统不一致,在同时使用PC和MAC的时候文件交换是个很大的问题。习惯了NTFS，我们都会选择在OSX开启其默认的NTFS的读写支持,来保证不同系统下的文件交互。可是在MAC OSX下开启ntfs的读写支持后会出现很多不兼容、不稳定的情况。今天我这块硬盘（NTFS）在MAC OSX 下就出现文件奇迹般消失的问题。 

#开启MAC OSX的NTFS支持
因为Apple是没有微软的NTFS文件系统的版权的，所以默认下是没有开启的，开启姿势如下： 1.在osx中 ntfs设备是通过一个程序来挂载的，这个程序是 /sbin/mount_ntfs， 我们要修改它，所以先备份起来
sudo mv /sbin/mount_ntfs /sbin/mount_ntfs.ori
2.然后重新写一份/sbin/mount_ntfs，就一行命令可以了，新建一个空白的/sbin/mount_ntfs，在里面加上下面两行
#!/bin/bash
/sbin/mount_ntfs.ori -o rw,nobrowse "$@"
3.然后改一下这个脚本的执行权限，就可以了
sudo chmod 755 /sbin/mount_ntfs

#找回消失的文件 
在Windows下检查NTFS硬盘
chkdsk g: /f
检查完毕后，如有解决诸多孤立文件和恢复文件等字样，完毕后就能在硬盘找到消失的文件了。