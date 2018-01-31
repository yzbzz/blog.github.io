---
title: hexo
date: 2016-08-22 16:11:51
tags: study
---

### Hexo简介

> 官网：https://hexo.io/zh-cn/

主题：[yilia](https://github.com/litten/hexo-theme-yilia)

MARKDOWN EDITOR: [typora](https://typora.io)

### TIPS

```java
npm install hexo-deployer-git --save
```

其它命令不确定，部署这个命令一定要用git bash，否则会提示

`Permission denied (publickey).`

推荐看此博客：http://www.cnblogs.com/liuxianan/p/build-blog-website-by-hexo-github.html

### Hexo常用命令

- 安装Hexo

  `sudo npm install -g hexo`

- 初始化

  `hexo init`

- 生成静态页面

  `hexo generate`(hexo g也可以)

- 本地启动

  `hexo server`

- 执行配置命令上传到github

  `hexo deploy`

- 新建文章

  `hexo new 'postName'`

- 新建页面

  `hexo new page 'pageName'`

- 查盾帮助

  `hexo help`

- 查看Hexo的版本

  `hexo version`

- 每次部署的步骤，可按以下三步来进行

  hexo clean

  hexo g

  hexo d
