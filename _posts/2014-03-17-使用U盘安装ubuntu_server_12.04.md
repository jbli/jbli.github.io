---
layout: post
title:  "使用U盘安装ubuntu server 12.04"
date:   2014-03-17 18:14:40
categories:  ubuntu 
tags:  U盘 安装 
---
##背景
用U盘安装ubuntu经常会遇到如下报错:

![安装报错](/images/ubuntu_install_err01.jpg)

##准备
1. 下载ubuntu server的ios
2.  usb写镜像的工具，[YUMI Multiboot USB Creator](http://www.pendrivelinux.com/yumi-multiboot-usb-creator/)和 [unetbootin](http://unetbootin.sourceforge.net/) 这两个差不多，都支持ubuntu server
3. window7系统环境
4. U盘

##刻录
如图所示

![刻录](/images/YUMI-Multiboot-USB-Creator.png)

出现问题:

```
An error(1) occurred while executing syslinux. Your USB drive won’t be bootable.
```

解决办法:

1. 关闭win7下的杀毒软件
2.  U盘提前格式化，在用YUMI制作镜像时不要勾选"Format H:Drive(Erase Content)?"

##安装
选择第二行<br>
![安装界面](/images/YUMI-Boot-Menu.png)

##所遇到问题
系统启动时卡在 initranfs
![initranfs](/images/ubuntu_grub_initramfs.jpg)

输入 exit 后回车，可以进入系统<br>
把/boot/grub/grub.cfg中

```
linux   /vmlinuz-3.11.0-18-generic root=/dev/mapper/localhost--vg-root ro   quiet splash $vt_handoff
```
加入rootdelay=90， 变为

```
linux   /vmlinuz-3.11.0-18-generic root=/dev/mapper/localhost--vg-root ro   quiet splash rootdelay=90 $vt_handoff
```
