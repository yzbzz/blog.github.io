---
title: macOS配置android环境变量(adb等)
date: 2018-02-01 11:39:14
tags: study
---

### 步骤

1.把开终端(terminal)，不知道地方的，可以使用`Spotlight`搜索

2.打开后输入以下命令进入根目录，命令如下
  ```
cd
  ```

3.更新`.bash_profile`文件，可以使用`ls -a`命令查看是否存在文件，不存在的话使用以下命令创建
  ```
touch .bash_profile
  ```

4.打开`.bash_profile`文件，输入下面命令后，会弹出文本编辑 框

  ```
open -e .bash_profile
  ```

5.编辑`.bash_profile`文件如下

  ```python
ANDROID_HOME=/Users/apple/Library/Android/sdk
export PATH=$PATH:$ANDROID_HOME/tools
export PATH=$PATH:$ANDROID_HOME/platform-tools
  ```

>  其中的apple替换为你的用户名


6.更新`.bash_profile`

  ```
  source .bash_profile
  ```

