---
title:  Ubuntu开启BBR
date: 2018-03-30 11:02:35
tags: study
---

原文地址：[Ubuntu开启BBR加速Shadowsocks](https://www.flyzy2005.com/fan-qiang/shadowsocks/ubuntu-bbr-shadowsocks/)

### 升级内核

ubuntu开启BBR加速需要内核高于4.9，先检查内核版本:

```python
uname -a
```

如果版本高于4.9，那就可以直接开启BBR，否则需要先升级内核（需要root权限）

1.首先查看系统是32位还是64位

```bash
getconf LONG_BIT
```

2.去[官网](http://kernel.ubuntu.com/~kernel-ppa/mainline/)最新的程序包，这里以`v4.15/`为例

```bash
wget http://kernel.ubuntu.com/~kernel-ppa/mainline/v4.15/linux-image-4.15.0-041500-generic_4.15.0-041500.201802011154_amd64.deb
```

3.执行下面的命令进行升级

```bash
dpkg -i linux-image-4.15.0-041500-generic_4.15.0-041500.201802011154_amd64.deb
```

4.更新grub引导装入程序

```
update-grub
```

5.重启机器

```
reboot
```

重启后查看自己的内核版本就会发现更新到4.15了

### 开启TCP BBR加速

1.修改系统变量

```bash
echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf
echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.conf
```

如查执行上述命令时显示拒绝访问，则可以尝试使用如下命令：

```bash
sudo bash -c 'echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf'
sudo bash -c 'echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.conf'
```

2.保存生效

```bash
sysctl -p
```

3.查看是否开启成功

```bash
sysctl net.ipv4.tcp_available_congestion_control
```

如果返回:`net.ipv4.tcp_available_congestion_control = reno cubic bbr`，那么表示`BBR`开启成功

也可以执行`lsmod | grep bbr`来查看是否开启成功