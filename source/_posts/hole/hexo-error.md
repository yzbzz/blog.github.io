---
title: Hexo错误集锦
date: 2018-01-30 10:38:13
tags: hole
---

使用Hexo部署的过程中报出以下错误。

```python
Hexo: TypeError: Cannot set property 'lastIndex' of undefined
```

解决方法：打开主工程的配置文件_config.yml(非主题里面的配置文件)，找到`auto_detect`，设置为false即可 



ERROR Deployer not found: git

```
npm install hexo-deployer-git --save
```

npm i hexo-generator-json-content --save