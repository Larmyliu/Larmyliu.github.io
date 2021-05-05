---
title: hexo搭建博客遇到的一些坑
date: 2020-07-30 22:06:35
tags: hexo
categories: hexo
---

 1. 创建仓库的名字一定要是`用户名.github.io`，我看错了直接创建了用户名仓库，就会报找不到仓库
 2. `_config.yml`如果配置的是`https`，每部署一次都会要你输入用户名和密码，换成ssh就不会，例如


```javascript
deploy:
  type: git
  repo: git@github.com:Larmyliu/Larmyliu.github.io.git
  branch: master
```
3.更换主题后报错`extends includes/layout.pug block content include includes/recent-posts.pug include`
执行命令

```javascript
npm install --save hexo-renderer-jade hexo-generator-feed hexo-generator-sitemap hexo-browsersync hexo-generator-archive

hexo c
hexo g
```
即可