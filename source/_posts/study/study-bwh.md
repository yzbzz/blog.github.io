---
title: 多图详解搬瓦工(Bandwagon)购买流程
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

首先进入官网

![bwh_home](/img/study/bwh_home.jpg)

点击`VPS Hosting`，进入到VPS Hosting页面，我们先选择最便宜的一款，购买完成后可以根据自身情况进行升降服务器，后续会介绍如何升降。KVM是新的架构，OVZ是老的架构，大家要选Order KVM，不要选错了。

> 大家不知道选哪个的话，可以网上搜一搜，推荐个地方：
>
> http://banwagong.cn/gonglue.html
>
> https://zhuanlan.zhihu.com/p/32811900

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

### 科学上网

传送门：[合适懒人的搬瓦工+ShadowsocksX教程](/2018/02/26/study/study-bwh-ssr/)

### VPS服务器升降

还记得文章上述的这个界面吗

![bwh_home_services_detail](/img/study/bwh_home_services_detail.jpg)

点击`Manage`，进入到服务器管理页

![bwh_service_info](/img/study/bwh_service_info.jpg)

点击后，进入下面的界面，大家可以按需选择

![bwh_service_detail](/img/study/bwh_service_detail.jpg)