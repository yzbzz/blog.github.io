---
title: 使用CentOS7安装配置Jenkins
date: 2018-01-29 11:51:08
tags: study
---

### 安装Java

`Jenkins`依赖`Java`，如果你的系统没有安装的话，需要先安装`Java`，已安装的话，可以忽略。使用以下命令

安装`Java`

```python
yum install java
```

查看`Java`版本

```
java -version
```

<!-- More -->

如果显示以下项，表示已安装

```
openjdk version "1.8.0_161"
OpenJDK Runtime Environment (build 1.8.0_161-b14)
OpenJDK 64-Bit Server VM (build 25.161-b14, mixed mode)
```

如果你显示的是类似以下的界面

```python
java version "1.5.0" 
gij (GNU libgcj) version 4.3.3
```

你需要先删除`Java`，重新安装`OpenJDK`，因为CentOS的系统，如果`java`版本是`GCJ`的版本，会导致Jenkins不工作(https://issues.jenkins-ci.org/browse/JENKINS-743)

##### 使用如下命令

1. **先删除旧的`java`**

```python
yum remove java
```

2. **查看`OpenJDK`版本**

```
yum search openjdk
```

显示如下界面

```python
...
java-1.8.0-openjdk.i686 : OpenJDK Runtime Environment
java-1.8.0-openjdk.x86_64 : OpenJDK Runtime Environment
java-1.8.0-openjdk-accessibility.i686 : OpenJDK accessibility connector
java-1.8.0-openjdk-accessibility.x86_64 : OpenJDK accessibility connector
...
```

3. **安装`OpenJDK`**

```python
yum install java-1.8.0-openJDK
```

### 安装Git

如果你需要使用`git`，使用以下命令进行安装(-y表示所有安装时的确认项都选y)

```
yum install -y git
```

### 安装Jenkins

首先我们进入到`Jenkins`官网，传送门：https://jenkins.io/download/

这时我们会看到两栏，左边的`Long-term Support (LTS)`是12周发布一版，右边的`Weekly`是每周发布一个版本。

这里我们以`Weekly`为例进行安装，拖动页面找到你自己的系统并点击

CentOS传送门：https://pkg.jenkins.io/redhat/

在这个界面我们可以看到官网教我们安装的步骤，简单说明一下

##### 第一步，输入以下命令

**拉取`Jenkins`库**

```python
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat/jenkins.io.key
```

> 注意文章所说的2点
>
> 1.如果您之前从Jenkins中导入了密钥，那么“rpm—import”将会失败，因为您已经有了一个密钥。请忽略这一点，继续前进。
>
> 2.您需要显式地安装Java运行时环境，因为Oracle的Java rpm是不正确的，并且无法注册为提供Java依赖项。因此，在Java中添加显式的依赖项要求将强制安装OpenJDK JVM。(以述步骤已安装了`java` ，忽略此条)

你可能会出现以下现象

```python
[root@host ~]# sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat/jenkins.repo
sudo: wget: command not found
```

这里，你需要先安装wget

```python
yum install -y wget
```

**安装`Jenkins`包**

```python
yum install -y jenkins
```

**更新`Jenkins`**

```python
yum update jenkins
```

**查看`Jenkins`端口**

```python
cat /etc/sysconfig/jenkins | more
```

**查找`JENKINS_PORT`，一般为`"8080"`，一屏显示不下的话，按回车查看，按`control+c`退出**

```python
JENKINS_PORT="8080"
```

**开启`Jenkins`服务**

```python
service jenkins start
```

浏览器访问: `IP地址:8080`

**关闭`Jenkins`服务**

```python
service jenkins stop
```

### 配置`Jenkin`

首先使用浏览器打开`Jenkin`(ip:8080)，如果你是第一次安装，会出现以下界面

![centenos_jenkins_first](/img/study/jenkins/centenos_jenkins_first.jpg)

我们去图中的地址去查看密码

```
cat /var/lib/jenkins/secrets/initialAdminPassword
```

选中密码，按`command+c`复制，然后`command+v`到浏览器`管理员密码`那一栏，点击继续，会出现

![jenkins_install_plugin_first](/img/study/jenkins/jenkins_install_plugin_first.jpg)

我们点击左边的`安装推荐的插件`，等待安装完成，完成后会出现

![jenkins_install_setting](/img/study/jenkins/jenkins_install_setting.jpg)

设置完你的管理员用户，点`保存并完成`进入下个界面，点`准备就绪`，进入下个界面

![jenkins_install_complete](/img/study/jenkins/jenkins_install_complete.jpg)



**OK，Jenkins安装配置完成！**

### 更多操作

传送门：https://wiki.jenkins.io/display/JENKINS/Installing+Jenkins+on+Red+Hat+distributions