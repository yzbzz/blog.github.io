---
title: 使用CentOS7安装GitLab
date: 2018-02-01 17:22:33
tags: study
---

### 安装GitLab

首先我们进入官网：https://about.gitlab.com/installation/

找到`CentOS 7`，点击展开

**1.安装和配置必要的依赖项**

```python
sudo yum install -y curl policycoreutils-python openssh-server 
sudo systemctl enable sshd 
sudo systemctl start sshd sudo firewall-cmd --permanent --add-service=http 
sudo systemctl reload firewalld 
```

安全完成后，输入以下命令安装`Postfix`，用于发邮件

```python
sudo yum install postfix 
sudo systemctl enable postfix 
sudo systemctl start postfix 
```

**2.安装GitLab仓库和包**

先添加`GitLab`仓库

```python
curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.rpm.sh | sudo bash 
```

再添加`GitLab`包，把`http://gitlab.example.com`改为你自己想要访问的`GitLab`地址，例如：

`http://100.10.10.10`这样的，最好填你服务器的地址

```python
sudo EXTERNAL_URL="http://gitlab.example.com" yum install -y gitlab-ee 
```

**3.执行下面命令使配置生效**

```python
gitlab-ctl reconfigure
```

**4.服务命令**

```python
gitlab-ctl start
```

**5.访问页面**

http://地址

如果出现以下字样，是因为CentOS7默认使用了firewalld防火墙，你可以直接关闭掉(`systemctl stop firewalld`)

```python
					502
Whoops, GitLab is taking too much time to respond.
```

