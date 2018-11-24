---
title: study-gitlab
date: 2018-09-11 11:28:43
tags:
---

### 备份与还原

```bash
gitlab-rake gitlab:backup:create
```

发果出现下面的提示

```bash
Errno::EACCES: Permission denied @ dir_s_mkdir - /var/opt/gitlab/backups
```

表明没有执行`mkdir`的权限 

先使用`ls -al`命令查看`backups`文件夹的权限

```
drwx------   2 root          root       4096 Sep 11 11:04 backups
```

判断是执行实际操作的gitlab相关用户：git，没有足够的权限。依次执行以下命令

```bash
[root@localhost_xx.xx.xx.xxx gitlab]# mkdir /var/opt/gitlab/backups
[root@localhost_xx.xx.xx.xxx gitlab]# mkdir /var/opt/gitlab/backups
[root@localhost_xx.xx.xx.xxx gitlab]# mkdir /var/opt/gitlab/backups
```

> 如果之前使用了 *rm -rf backups* 命令移除整个backups文件夹，所以需要再次mkdir。如果该文件夹已经存在，就不需要mkdir了

以上命令执行完成后，再次 *ls -al*，发现backups的权限已经发生改变了

```
drwx------   2 git          root       4096 Sep 11 11:04 backups
```

再次执行backup或者restore，已经可以正常运行，问题解决。