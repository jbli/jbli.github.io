---
layout: post
title:  "搭建ubuntu的内部apt源"
date:   2014-04-03 14:14:40
categories: ubuntu
tags:  apt源
---

##好处
一 安装软件速度极快<br> 
二 多种deb源汇聚到一起， 除了ubuntu官方的deb, 还可以把第三方提供的deb，也可以自己把任意软件打成deb,放到自己的的内部源中，包括公司要发布的新程序。<br> 
三 只需要150G的磁盘空间， 第一次从源站下载包需要花费大概两天时间，以后每天更新操作只需要在crontab中运行一下脚本，维护非常方便。<br> 

##具体操作
###安装和配置apt-mirror

```
apt-get install apt-mirror
```
修改/etc/apt/mirror.list

```
############# config ##################
# Need gpg for apt source
########
set base_path    /bigd/baseSrv/apt-mirrors

set mirror_path  $base_path/mirror
set skel_path    $base_path/skel
set var_path     $base_path/var
#set cleanscript $var_path/clean.sh
#set defaultarch  <running host architecture>
#set postmirror_script $var_path/postmirror.sh
set run_postmirror 0
set nthreads     10
set _tilde 0
#
############# end config ##############

deb http://archive.ubuntu.com/ubuntu precise main restricted universe multiverse
deb http://archive.ubuntu.com/ubuntu precise-security main restricted universe multiverse
deb http://archive.ubuntu.com/ubuntu precise-updates main restricted universe multiverse
deb-i386 http://archive.ubuntu.com/ubuntu precise main restricted universe multiverse
deb-i386 http://archive.ubuntu.com/ubuntu precise-security main restricted universe multiverse
deb-i386 http://archive.ubuntu.com/ubuntu precise-updates main restricted universe multiverse

deb-src http://archive.ubuntu.com/ubuntu precise main restricted universe multiverse
deb-src http://archive.ubuntu.com/ubuntu precise-security main restricted universe multiverse
deb-src http://archive.ubuntu.com/ubuntu precise-updates main restricted universe multiverse

clean http://archive.ubuntu.com/ubuntu
```
其中 “set nthreads ”来决定用多少个线程来下载

然后就可以通过下面命令运行,就开始下载deb包到指定的目录。第一次下载需要一两天时间。

```
apt-mirror &
```

###web服务器配置
在nginx的配置中增加如下内容

```
server {
        listen       9001;
        server_name 10.100.30.10;
        location ~ ^/mirror {
            root   /bigd/baseSrv/apt-mirrors;
            autoindex on;
            index  index.html index.htm;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
```
启动nginx，监听9001端口

###客户端配置
在客户端/etc/apt/sources.list内容更改为

```
deb http://10.100.30.10:9001/mirror/archive.ubuntu.com/ubuntu/ precise main restricted universe multiverse
deb http://10.100.30.10:9001/mirror/archive.ubuntu.com/ubuntu/ precise-security main restricted universe multiverse
deb http://10.100.30.10:9001/mirror/archive.ubuntu.com/ubuntu/ precise-updates main restricted universe multiverse
deb-src http://10.100.30.10.11:9001/mirror/archive.ubuntu.com/ubuntu/ precise main restricted universe multiverse
deb-src http://10.100.30.10:9001/mirror/archive.ubuntu.com/ubuntu/ precise-security main restricted universe multiverse
deb-src http://10.100.30.10:9001/mirror/archive.ubuntu.com/ubuntu/ precise-updates main restricted universe multiverse
```
然后更新操作

```
apt-get update
```
这样，就可以在客户端使用我们自己搭建的源了。


##加入常用的第三方源
###加入percona源
参考[这里](http://www.percona.com/doc/percona-xtrabackup/2.1/installation/apt_repo.html)

在服务器端执行下面命令加入该源的key
```
apt-key adv --keyserver keys.gnupg.net --recv-keys 1C4CBDCDCD2EFD2A
```
修改/etc/apt/mirror.list，加入

```
deb http://repo.percona.com/apt precise main
deb-i386 http://repo.percona.com/apt precise main
deb-src http://repo.percona.com/apt precise main
```
同样需要运行

```
apt-mirror &
```

在客户端的/etc/apt/sources.list加入

```
deb http://10.100.30.10:9001/mirror/repo.percona.com/apt precise main
deb-src http://10.100.30.10:9001//mirror/repo.percona.com/apt precise main
```
执行

```
apt-key adv --keyserver keys.gnupg.net --recv-keys 1C4CBDCDCD2EFD2A
```
然后更新

```
apt-get update
```

###其它常用第三方源
- [puppet](https://apt.puppetlabs.com/README.txt)
- [docker](http://docs.docker.io/en/latest/installation/ubuntulinux/)

###加入个人的deb源
在服务器上需要安装 dpkg-de

```
apt-get install dpkg-dev  
gpg --gen-key 
```
在客户端/etc/apt/sources.list

```
deb http://10.10.130.11:9001/mirror/apt.snowballfinance.com/ /
```
执行

```
apt-get update
```
会报如下错误

```
W: GPG error: http://10.10.130.11  Release: The following signatures couldn't
be verified because the public key is not available: NO_PUBKEY
CE26643CD26F3745
```
在服务器端

```
gpg --list-keys
```
会得到如下内容
```
----------------------------------
pub   2048R/D26F3745 2014-04-14
uid                  apt.snowballfinance.com <lijb@xueqiu.com>
sub   2048R/2446DD43 2014-04-14
```

执行

```
gpg -a --export D26F3745 |  apt-key add -  
```

然后把服务器端的把/etc/apt/trusted.gpg 拷到客户端，再在客户端执行

```
apt-get update
```

注意：
以后对个人源的包 加入一个的包或删除一个包，都要以特定用户重新执行如下

```
dpkg-scanpackages ./ >Packages
apt-ftparchive release ./ >Release
gpg --clearsign -o InRelease Release
gpg -abs -o Release.gpg Release
```
如果报如下错,注意检查该用户家目录下是否.gnupg

```
gpg: no default secret key: secret key not available
gpg: Release: clearsign failed: secret key not available
```