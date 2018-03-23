---
title: shadowsocks多用户配置
date: 2018-01-26 15:55:52
tags: study
---

### 前言

之前一篇文章，我们讲了[合适懒人的搬瓦工+ShadowsocksX教程](/2018/02/26/study/study-bwh-ssr/)，我们使用的是`搬瓦工`自己的服务进行安装的，还没有看的同学请先看下。本篇主要讲解如何使用命令行的方式进行安装`shadowsocks`，配置单用户和多用户。

如何进入服务器: [使用SSH客户端(Termius)连接服务器](/2018/01/30/study/study-centos-ssh/)

<!-- More -->

### shadowsocks服务器配置

这里我们安装的是python版本，首先进入官网:https://github.com/shadowsocks/shadowsocks/tree/master

进入后拖动页面，找到自己的服务器版本，我们这里以`CentOS`为例:

#### 安装shadowsocks

```python
yum install python-setuptools && easy_install pip
pip install git+https://github.com/shadowsocks/shadowsocks.git@master
```

安装完成后，打开 https://github.com/shadowsocks/shadowsocks/wiki/Configuration-via-Config-File

我们可以看到官网的一些描述，这里简单说明一下：

**第一步**，创建一个`shadowsocks.json`文件并打开，使用以下命令

```python
vi /etc/shadowsocks.json
```

**第二步**，按`i`键进入编辑模式，当按下`i`时，这时左下角会显示 `INSERT`字样，表示当前处于编辑模式。输入以下内容(因为是命令行模式，所以大家可以先使用文本编辑器，写好下面的文字，然后复制一下，贴到你的命令行)

**单用户配置**

```python
{
    "server":"my_server_ip",
    "server_port":8388,
    "local_address": "127.0.0.1",
    "local_port":1080,
    "password":"mypassword",
    "timeout":300,
    "method":"rc4-md5",
    "fast_open": false
}
```

> `my_server_ip `填你的服务器地址
>
> 8388填你的端口(自定义的，可能不更改，保留8388)
>
> `mypassword`填写你要设置的密码

**多用户配置**

```python
{
    "server":"my_server_ip",
    "local_address":"127.0.0.1",
    "local_port":1080,
    "port_password":{
    	"端口1":"密码1",
    	"端口2":"密码2",
    	"端口3":"密码3"
    },
    "timeout":600,
    "method":"rc4-md5",
  	"fast_open": false
}
```

> `my_server_ip `填你的服务器地址
>
> `端口1、密码1`填你要分配给客户端的端口和密码，比如'8388':"123456"，后面的端口、密码同理

**注意**：`method`一栏，默认是`aes-256-cfb`，这里改为`rc4-md5`，这样上网会快一些，相应的客户端加密也要改成`rc4-md5`，别忘了

这里设置的项都是给你客户端连接时使用的

**第三步**，开启或关闭服务

使用以下命令

开启服务

```python
ssserver -c /etc/shadowsocks.json -d start
```

关闭服务

```python
ssserver -c /etc/shadowsocks.json -d stop
```

**开机自启动**

```
vi /etc/rc.local
```

在`rc.local`中添加

```
sudo ssserver -c /etc/shadowsocks.json -d start
```



大功告成，愉快的玩耍吧(^^)