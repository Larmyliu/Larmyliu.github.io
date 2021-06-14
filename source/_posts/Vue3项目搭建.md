---
title: Vue3项目搭建
date: 2021-01-15 16:39:19
tags:
	- 前端
	- Vue3
categories:前端
---

此文将介绍如何搭建一个Vue3项目
基于[官方文档](https://v3.cn.vuejs.org/guide/installation.html)
### 条件

 - Node 8+;
 - vue-cli 4+
 - npm 6.1+

可以再cmd中输入以下指令查看当前版本
```javascript
node -v
vue -V
npm -v
```

对于 Vue 3，你应该使用 npm 上可用的 `Vue CLI v4.5` 作为 `@vue/cli`。要升级，你应该需要全局重新安装最新版本的 `@vue/cli`：

```javascript
// 卸载
npm uninstall vue-cli -g
// 安装
npm install @vue/cli -g
```
也可以使用`cnpm`下载，这样速度会快很多

### 创建项目
#### CLI创建项目
把项目创建在myproject目录下
```javascript
vue create myproject 
```
创建完成后可以在`8080`端口访问
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210115162509420.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210115162630168.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70)
#### vite创建项目
尤大在vue3发布的时候着重说`vite`，所以也可以用`vite+vue3`来构建项目
官网讲的很详细

```javascript
 npm init vite-app <project-name>
 cd <project-name>
 npm install
 npm run dev
```
可以再`3000`端口启动项目
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210115163020873.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210115163034864.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70)
亲测证明vite启动的速度更快
当然有朋友喜欢用`vue ui`来创建项目，其实也是一样的可以自己来配置一些插件
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210115163423467.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70)