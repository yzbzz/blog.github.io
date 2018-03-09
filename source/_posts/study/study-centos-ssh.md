---
title: 使用SSH客户端(Termius)连接服务器
date: 2018-01-30 14:20:34
tags: study
---

### 前言

我们买的是远端服务器，没有用户界面的，只能通过命令行去操作我们的系统，那么如何进入我们的服务器呢

<!-- More -->

### SSH连接服务器

**有二种方式：**

**第一种**

点击搬瓦工的`KiwiVM Control Panel`，进入到服务器控制面板，点击左侧的`Roo shell - basic`进入服务器

![bwh_kiwivm_command](/img/study/command/bwh_kiwivm_command.jpg)

**第二种**

如果嫌每次都要从浏览器进入比较麻烦，可以选择使用`SSH`连接我们的服务器

先下载`ssh`客户端：http://www.termius.com

下载完成后打开

![termius_connect](/img/study/command/termius_connect.jpg)

1. 界面上看到`ssh user@hostname -p port`这行字没，你可以直接使用下面的方式进行连接

```python
ssh root@170.1.2.3 -p 8388
```

> hostname: 这里我写的是170.1.2.3，你需要填写你的服务器ip
>
> port: 同理，你需要填写你的服务器端口

点击`connect`后，会弹出框，要你输入服务器密码，输入正确后进入服务器

2. 或者你可以点击右下角的`+ New Host`，输入你的配置信息，点击保存开始连接

> Address：服务器的ip地址，类似这样: 170.1.2.3
>
> Port: 服务器的端口，类似这样: 8388
>
> Username: 这里填`root`
>
> Password：这里为你安装服务器时的密码，就是之前你使用搬瓦工重装系统后的服务器密码

**如果你忘记了服务器密码怎么办，没有关系，从下面步骤重新获取**

进入到搬瓦工服务器控制面板，先到`Main controls`点击`stop`停掉你的服务器，再点击左侧的`Root password modification`从新获取，获取完成后，别忘了到`Main controls`点击`start`打开你的服务器