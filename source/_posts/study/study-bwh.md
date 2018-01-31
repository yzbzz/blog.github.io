---
title: 使用搬瓦工+ShadowsocksX进行科学上网
date: 2018-01-25 10:52:11
tags: study
---

（温馨提示：本文图片较多，请在WIFI情况下阅读）

### 前言

在天朝，因为qian的原因，很多外网是访问不了的。身为程序员，很多时候是需要访问`google`进行问题搜索，这时候就需要fan qiang进行科学上网了。

可能有相当一部分人是买的VPN进行科学上网，但VPN一般比较贵，也不稳定，不如自己搭建VPS（相当于自己买一台海外服务器），一方面可以可以学习一下Linux，一方面还可以科学上网，还比VPN便宜不少。

<!-- More -->

### VPS购买

其实VPS搭建还是比较简单的，本文使用搬瓦工(Bandwagon)，搬瓦工对新手比较友好，而且官方支持ShadowsocksX，最重要的是支持`支付宝`，省去了很多麻烦，官网：https://bwh1.net

首选进入官网

![bwh_home](/img/study/bwh_home.jpg)

点击`VPS Hosting`，进入到VPS Hosting页面，我们先选择最便宜的一款，购买完成后可以根据自身情况进行升降服务器，后续会介绍如何升降。KVM是新的架构，OVZ是老的架构，大家要选Order KVM，不要选错了。

> 大家不知道选哪个的话，可以网上搜一搜，推荐个地方：
>
> http://banwagong.cn/gonglue.html
>

![bwh_host](/img/study/bwh_host.jpg)

点击`Order KVM`，进入到详情页面，因为`搬瓦工`的VPS是经常变换的，可能你看到的页面和我的不太一样，不过没关系，下面我们会说到。

![bwh_order_cycle](/img/study/bwh_order_cycle.jpg)

因为我选的这个VPS，只支持年付，所有`Billing CyCle`只有一个选项。你的VPS可能是下面的这种情况，我这里的截图是20G VPS的，因为10G VPS的只支持年付，没法截下面的图

![bwh_order_cycle_more](/img/study/bwh_order_cycle_more.jpg)

> 注意：
>
> Monthly 表示月付，就是使用一个月
>
> Quarterly 表示季付，就是使用三个月
>
> Semi-Annually 表示半年
>
> Annually 表示一年

建议大家选择一年，相对便宜，选完`Billing Cycle`，接下来选机房

![bwh_order_location](/img/study/bwh_order_location.jpg)

这里我们选择洛杉矶机机房，上面的第二项(后续可以在控制面板切换)，选完之后点击下方的`Add to Cart`，进入到`订单汇总页面`

![bwh_order_detail](/img/study/bwh_order_detail.jpg)

这里我们使用`BWH1ZBPVK`这个优惠码(随时失效，大家可以百度搜一搜优惠码)，输入完优惠码后，点击右边的`Validate Code>>`，出现下面的界面，表示优惠码有效

![bwh_order_detail_pc](/img/study/bwh_order_detail_pc.jpg)

确认没问题后，点击`Checkout`去结账

![bwh_order_complete](/img/study/bwh_order_complete.jpg)

如果有账号的话，点击`Click me to login`进行登录。没有的话，填写下面的项，后面标了`红色`字体的尽量都填上，最好真实的，防止被误认为`订单欺诈`

> 重要提示：不要填中文，使用拼音代替。比如：姓名叫李白，LastName填`li`，FirstName填`bai`
>
> ，还有State/Region一栏，要先选择Country，这样State/Region才会变

输入完成之后，点击`Update`。

接下来选择支付方式，我们这里选择`支付宝`

![bwh_order_pay](/img/study/bwh_order_pay.jpg)

点击`Complete Order`，会进入到`支付宝`支付界面

![bwh_order_pay_complete](/img/study/bwh_order_pay_complete.jpg)

扫描完成支付(不要扫图中的码哦)，支付成功后会收到邮件通知，里面会有你的服务器ip地址等信息。

### VPS服务器搭建

通过上面的步骤，我们已经完成了VPS的购买，接下来我们开始搭建自己的服务器

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

**重要：如果希望自行安装shadowsocks(可以配置单、多用户)，可以选择`centos-7-x86_64-bbr`，建议大家选择自行安装([shadowsocks多用户配置](http://localhost:4000/2018/01/26/study/study-bwh-ss/))，不喜欢折腾的，可以继续往下看**

![bwh_kiwi_new_os](/img/study/bwh_kiwi_new_os.jpg)

点击`reload`，进行重装系统 ，同时会进入下面的界面

![bwh_kiwi_new_os_info](/img/study/bwh_kiwi_new_os_info.jpg)

这里的`端口`和`服务器密码`需要记下来，方便以后使用ssh连接服务器使用

如何进入服务器: [使用SSH客户端(Termius)连接服务器](/2018/01/30/study/study-centenos-ssh/)

稍等一会，刷新页面，点击左侧的`Main controls`，出现下面的界面，表示系统安装成功

![bwh_kiwi_main_new](/img/study/bwh_kiwi_main_new.jpg)

接下来，我们要安装`ShadowsocksX`服务

### ShadowsocksX服务安装

**重要：希望自行安装shadowsocks的用户可以跳过此步，传送门:[shadowsocks多用户配置](http://localhost:4000/2018/01/26/study/study-bwh-ss/)**

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

### VPS服务器升降

还记得文章上述的这个界面吗

![bwh_home_services_detail](/img/study/bwh_home_services_detail.jpg)

点击`Manage`，进入到服务器管理页

![bwh_service_info](/img/study/bwh_service_info.jpg)

点击后，进入下面的界面，大家可以按需选择

![bwh_service_detail](/img/study/bwh_service_detail.jpg)