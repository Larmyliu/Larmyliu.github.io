---
title: vue-cli4.5创建Vue3.x引入element-ui
date: 2021-01-18 19:37:21
tags:
	- 前端
	- element
	- Vue3
categories: 前端
---

在Vue项目中常使用的一个`UI`库就是`element-ui`了
# **注意**
目前`element-ui`只支持Vue2.6以下的版本，如果想在Vue3.0使用这个组件库，就要使用`element-plus`
[element-plus官网](https://element-plus.gitee.io/#/zh-CN/component/quickstart)
### 步骤
在Vue3项目根目录下	
```javascript
vue add element-plus
npm install vue-cli-plugin-element-plus
```
### 全局引用
src/plugins/element.js

```javascript
import ElementPlus from 'element-plus'
import 'element-plus/lib/theme-chalk/index.css'
export default (app) => {
  app.use(ElementPlus)
}
```
就可以使用组件了
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210118191452728.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70)
### 按需引入
在下载`element-plus`后，会自动帮我们创建一个配置文件，放在`/src/plugins/element.js`，全局引入的时候代码放在这里，更能体现模块独立的思想
但是按需引入需要使用到`createApp()`返回的Vue实例，所以还是写在`/src/main.js`中

```javascript
import { createApp } from 'vue'
import App from './App.vue'
import 'element-plus/lib/theme-chalk/index.css'
import { ElButton, ElSelect } from 'element-plus';
const app = createApp(App)
app.use(ElButton)
app.use(ElSelect)
app.mount('#app')
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210118192403667.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70)
依旧可以使用