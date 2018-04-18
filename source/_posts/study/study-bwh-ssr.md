---
title: 合适懒人的搬瓦工+ShadowsocksX教程
date: 2018-02-26 14:09:55
tags: study
---

**建议大家自行安装shadowsocks(可以配置单、多用户)，传送门：[shadowsocks多用户配置](/2018/01/26/study/study-bwh-ss/)，不喜欢折腾的，可以继续往下看**

### VPS服务器搭建

通过之前一篇文章[多图详解搬瓦工(Bandwagon)购买流程](/2018/01/25/study/study-bwh/)，我们已经完成了VPS的购买，接下来我们开始搭建自己的服务器

我们先回到首页，点击`Client Area`，`Client Area`会出现在下面2个地方

![bwh_home_client_area_top](/img/study/bwh_home_client_area_top.jpg)

![bwh_home_client_ares](/img/study/bwh_home_client_ares.jpg)

点击后进入到下面的界面

![bwh_home_services](/img/study/bwh_home_services.jpg)

依次点击`Services` -> `My Services`，进入到下面的界面

![bwh_home_services_detail](/img/study/bwh_home_services_detail.jpg)

这里我们点击`KiwiVM Control Panel`进入到服务器控制面版。进入到服务器控制面版后，先把服务停掉(待会要重装系统)。

![bwh_kiwi_main](/img/study/bwh_kiwi_main.jpg)

搬瓦工提供无限次重装系统，这里我们先把服务停掉，重装一下系统。停掉服务后，点击左侧的`ShadowsocksR Server`，进入到`ShadowsocksR Server`界面，查看一下支持的`系统版本`

![bwh_kiwi_ss](/img/study/bwh_kiwi_ss.jpg)

从图中，我们可以看过，`ShadowsocksR Server`只支持`Centos 6 (32 or 64 bit)`的系统 ，所以我们需要重装一个`Centos 6 (32 or 64 bit) `的系统。

点击左边的`Install new OS`，选择`centos-6-x86_64-bbr`，进行重装系统

![bwh_kiwi_new_os](/img/study/bwh_kiwi_new_os.jpg)

点击`reload`，进行重装系统 ，同时会进入下面的界面

![bwh_kiwi_new_os_info](/img/study/bwh_kiwi_new_os_info.jpg)

这里的`端口`和`服务器密码`需要记下来，方便以后使用ssh连接服务器使用

如何进入服务器: [使用SSH客户端(Termius)连接服务器](/2018/01/30/study/study-centenos-ssh/)

稍等一会，刷新页面，点击左侧的`Main controls`，出现下面的界面，表示系统安装成功

![bwh_kiwi_main_new](/img/study/bwh_kiwi_main_new.jpg)

接下来，我们要安装`ShadowsocksX`服务

### ShadowsocksX服务安装

点击左侧的`ShadowsocksR Server`

![bwh_kiwi_ss_install](/img/study/bwh_kiwi_ss_install.jpg)

等待安装完成，出现下面界面，表示安装成功

![bwh_kiwi_ss_completed](/img/study/bwh_kiwi_ss_completed.jpg)

点击`Go back`，进入详情页，拖到到页面到`Step 2`。不要关闭这个界面，后面会用到

![bwh_kiwi_ss_info](/img/study/bwh_kiwi_ss_info.jpg)

至此，我们的ShadowsocksX服务安装完成，接下来，我们需要安装`ShadowsocksX`客户端

### ShadowsocksX客户端安装

这里以`Mac OS`系统为例，其它平台可以参考下面文章

##### Mac平台

点击https://github.com/shadowsocks/shadowsocks-iOS/wiki/Shadowsocks-for-OSX-Help

![bwh_ss_mac_os](/img/study/bwh_ss_mac_os.jpg)

点击图中标示的链接，会进入到一个下载页，点击`Download`开始下载，下载完成后安装，安装成功后，打开`ShadowsocksX`，打开后，会在菜单出现一个`纸飞机图标`，点击它

![bwh_ss_client](/img/study/bwh_ss_client.jpg)

因为我之前配过2个服务器，所以上面会显示2条记录。点击`打开服务器设定`

![bwh_kiwi_all](/img/study/bwh_kiwi_all.jpg)

照着填就可以了，填完点击`确定`。大功告成，可以科学上网啦！(依然上不了的，可以把客户端软件关了，再重开)

![bwh_ss_complete](/img/study/bwh_ss_complete.jpg)

##### Windows平台

点击https://github.com/shadowsocks/shadowsocks-windows/releases

![bwh_ss_windows](/img/study/bwh_ss_windows.jpg)

##### 其它平台

地址：https://github.com/shadowsocks?page=1

自己慢慢找吧(^^)