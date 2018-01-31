---
title: jenkins-android
date: 2017-07-07 11:37:13
tags: hole
---

之前用jenkins打包时，提示`Failed to find Build Tools revision xx.x.x` ，于是进入到`var/lib/jenkins/tools/android-sdk/tools/`下面，输入下面命令

```java
android list sdk -a
```

控制台没有反应，于是又试了下其他命令

<!-- More -->

```java
android -h
```

也没反应，瞬间懵逼。于是想看看其它命令是不是也没反应，切换到platform-tools目录去执行 adb 命令：

出现"libc.so.6: version 'GLIBC_2.15' not found"问题，才知道是由于glibc版本过低，升级glibc即可。

参照：http://blog.csdn.net/hnhuangyiyang/article/details/50392997

1.查看系统glibc支持的版本

```java
strings /lib64/libc.so.6 |grep GLIBC  
#查看当前glibc版本  
ll /lib64/libc.so.6   
lrwxrwxrwx. 1 root root 12 Oct  9  2014 /lib64/libc.so.6 -> libc-2.12.so  
```

2.下载glibc源码包 (http://ftp.gnu.org/gnu/glibc/)

```java
cd /usr/src/  
wget http://mirror.bjtu.edu.cn/gnu/glibc/glibc-2.15.tar.gz  
wget http://mirror.bjtu.edu.cn/gnu/glibc/glibc-ports-2.15.tar.gz　　　　　　#glibc插件 
```

3.解压安装升级glibc

```java
tar zxvf glibc-2.15.tar.gz  
tar zxvf glibc-ports-2.15.tar.gz  
mv glibc-ports-2.15 glibc-2.15/ports  
mkdir build  
cd build  
../glibc-2.15/configure  --prefix=/usr --disable-profile --enable-add-ons --with-headers=/usr/include --with-binutils=/usr/bin  
make -j4  
make install
```

4.验证(查看系统glibc支持的版本)

```java
strings /lib64/libc.so.6 |grep GLIBC 
```

5.误删libc.so.6解决办法(命令行执行如下：)

```java
#LD_PRELOAD=/lib/libc-2.12.so ln -s /lib/libc-2.12.so lib/libc.so.6 
```

升级完glibc之后，android 命令终于有反应了，继续执行

```
android list sdk -a
```

卡住了，想了想应该是被墙的原因，还好androd命令提供了代理参数：

```java
--proxy-host //设置代理host
--proxy-port //设置代理端口
```

网络找了找个国内的Android代理镜像服务器

```java
http://mirrors.neusoft.edu.cn 端口：80
```

接下来执行

```java
android list sdk --proxy-host mirrors.neusoft.edu.cn --proxy-port 80 -s --all
```

成功了

```java
Packages available for installation or update: 184
   1- Android SDK Tools, revision 25.2.5
   2- Android SDK Platform-tools, revision 26
   3- Android SDK Build-tools, revision 26
   4- Android SDK Build-tools, revision 25.0.3
   5- Android SDK Build-tools, revision 25.0.2
   6- Android SDK Build-tools, revision 25.0.1
   7- Android SDK Build-tools, revision 25
   8- Android SDK Build-tools, revision 24.0.3
   9- Android SDK Build-tools, revision 24.0.2
  10- Android SDK Build-tools, revision 24.0.1
  11- Android SDK Build-tools, revision 24
```

接下来执行下载sdk命令

```java
// --filter 3后面的数字3对面上面列表的项，这时表示下载 (3- Android SDK Build-tools, revision 26)
android update sdk --no-ui --filter 3 --proxy-host mirrors.neusoft.edu.cn --proxy-port 80 -s --all
```

安装的时候会提示是否接受`license`

类似

```
Do you accept the license 'android-sdk-license-c81a61d9' [y/n]:
```

输入`y`之后开始下载，等待安装成功，又可以愉快的玩耍了