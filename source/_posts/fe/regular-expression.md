---
title: 正则表达式
date: 2016-05-23 21:36:57
tags: fe
---

# 概述

> 熟练地掌握正则表达式，能够使你的开发效率得到极大的提升。

下面来梳理了一下常用的正则表达式

1. 校验密码强度

   密码的强度必须是包含大小写字母和数字的组合，不能使用特殊字符，长度在8-10之间

   ```java
   ^(?=.*\\d)(?=.*[a-z])(?=.*[A-Z]).{8,10}$
   ```

2. 校验中文

   字符串仅能是中文。

   ```java
   ^(?=.*\\d)(?=.*[a-z])(?=.*[A-Z]).{8,10}$
   ```

3. 由数字、26个英文字母或下划线组成的字符串

   ```java
   ^\\w+$
   ```

4. 校验E-Mail地址

   同密码一样，下面是E-mail地址合规性的正则检查语句。

   ```java
   [\\w!#$%&'*+/=?^_`{|}~-]+(?:\\.[\\w!#$%&'*+/=?^_`{|}~-]+)*@(?:[\\w](?:[\\w-]*[\\w])?
   ```

5. 校验身份证号码

   下面是身份证号码的正则校验。15或18位。

   15位

   ```java
   ^[1-9]\\d{7}((0\\d)|(1[0-2]))(([0|1|2]\\d)|3[0-1])\\d{3}$
   ```

   18位

   ```java
   ^[1-9]\\d{5}[1-9]\\d{3}((0\\d)|(1[0-2]))(([0|1|2]\\d)|3[0-1])\\d{3}([0-9]|X)$
   ```

6. 校验日期

   "yyyy-mm-dd"格式的日期校验，已考虑平闰年。

   ```java
   ^(?:(?!0000)[0-9]{4}-(?:(?:0[1-9]|1[0-2])-(?:0[1-9]|1[0-9]|2[0-8])|(?:0[13-9]|1[0-2])-(?:29|30)|(?:0[13578]|1[02])-31)|(?:[0-9]{2}(?:0[48]|[2468][048]|[13579][26])|(?:0[48]|[2468][048]|[13579][26])00)-02-29)$
   ```

7. 校验金额

   金额校验，精确到2位小数

   ```java
   ^[0-9]+(.[0-9]{2})?$
   ```

8. 校验手机号

   下面是国内 13、15、18开头的手机号正则表达式。（可根据目前国内收集号扩展前两位开头号码）

   ```java
   ^(13[0-9]|14[5|7]|15[0|1|2|3|5|6|7|8|9]|18[0|1|2|3|5|6|7|8|9])\\d{8}$
   ```

9. 提取URL链接

   下面的这个表达式可以筛选出一段文本中的URL

   ```java
   ^(f|ht){1}(tp|tps):\\/\\/([\\w-]+\\.)+[\\w-]+(\\/[\\w- ./?%&=]*)?
   ```
