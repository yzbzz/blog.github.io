---
title: Linux
date: 2018-01-25 10:52:11
tags: study
---

[jenkins](https://segmentfault.com/a/1190000007086764)

[shadowsocks](https://shadowsocks.blogspot.com/)

[shadowscocks](https://www.cnblogs.com/gne-hwz/p/6662000.html)

[shadowscocks](https://www.iwwenbo.com/0-1-shadowsocks-start/)

[android sdk](http://www.jb51.net/article/119183.htm)

[jenkins-github](https://www.jianshu.com/p/ee058d067fd9)

[jenkins-github](https://www.jianshu.com/p/38b2e17ced73)

### 拷贝文件

**scp**

scp不支持断点

使用`scp拷贝`远端文件到本地(使用`id_rsa.pub`)

```bash
scp -i id_rsa.pub root@xx.xx.xx.xx:/var/opt/gitlab/backups/ /Users/userNmae/Work/backups    
```

**rsync**

rsync支持断点

使用`rsync`(使用`id_rsa.pub`)拷贝远端文件到本地

```bash
rsync -avzP -e "ssh -i id_rsa.157" root@xx.xx.xx.xx:/var/opt/gitlab/backups/ /Users/userNmae/Work/backups/
```

> ```bash
> -v：显示rsync过程中详细信息。可以使用"-vvvv"获取更详细信息。
> -P：显示文件传输的进度信息。(实际上"-P"="--partial --progress"，其中的"--progress"才是显示进度信息的)。
> -n --dry-run  ：仅测试传输，而不实际传输。常和"-vvvv"配合使用来查看rsync是如何工作的。
> -a --archive  ：归档模式，表示递归传输并保持文件属性。等同于"-rtopgDl"。
> -r --recursive：递归到目录中去。
> -t --times：保持mtime属性。强烈建议任何时候都加上"-t"，否则目标文件mtime会设置为系统时间，导致下次更新
>           ：检查出mtime不同从而导致增量传输无效。
> -o --owner：保持owner属性(属主)。
> -g --group：保持group属性(属组)。
> -p --perms：保持perms属性(权限，不包括特殊权限)。
> -D        ：是"--device --specials"选项的组合，即也拷贝设备文件和特殊文件。
> -l --links：如果文件是软链接文件，则拷贝软链接本身而非软链接所指向的对象。
> -z        ：传输时进行压缩提高效率。
> -R --relative：使用相对路径。意味着将命令行中指定的全路径而非路径最尾部的文件名发送给服务端，包括它们的属性。用法见下文示例。
> --size-only ：默认算法是检查文件大小和mtime不同的文件，使用此选项将只检查文件大小。
> -u --update ：仅在源mtime比目标已存在文件的mtime新时才拷贝。注意，该选项是接收端判断的，不会影响删除行为。
> -d --dirs   ：以不递归的方式拷贝目录本身。默认递归时，如果源为"dir1/file1"，则不会拷贝dir1目录，使用该选项将拷贝dir1但不拷贝file1。
> --max-size  ：限制rsync传输的最大文件大小。可以使用单位后缀，还可以是一个小数值(例如："--max-size=1.5m")
> --min-size  ：限制rsync传输的最小文件大小。这可以用于禁止传输小文件或那些垃圾文件。
> --exclude   ：指定排除规则来排除不需要传输的文件。
> --delete    ：以SRC为主，对DEST进行同步。多则删之，少则补之。注意"--delete"是在接收端执行的，所以它是在
>             ：exclude/include规则生效之后才执行的。
> -b --backup ：对目标上已存在的文件做一个备份，备份的文件名后默认使用"~"做后缀。
> --backup-dir：指定备份文件的保存路径。不指定时默认和待备份文件保存在同一目录下。
> -e          ：指定所要使用的远程shell程序，默认为ssh。
> --port      ：连接daemon时使用的端口号，默认为873端口。
> --password-file：daemon模式时的密码文件，可以从中读取密码实现非交互式。注意，这不是远程shell认证的密码，而是rsync模块认证的密码。
> -W --whole-file：rsync将不再使用增量传输，而是全量传输。在网络带宽高于磁盘带宽时，该选项比增量传输更高效。
> --existing  ：要求只更新目标端已存在的文件，目标端还不存在的文件不传输。注意，使用相对路径时如果上层目录不存在也不会传输。
> --ignore-existing：要求只更新目标端不存在的文件。和"--existing"结合使用有特殊功能，见下文示例。
> --remove-source-files：要求删除源端已经成功传输的文件。
> ```