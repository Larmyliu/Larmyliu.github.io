刚接触小程序，发现在引入iconfont图标，原来是彩色的图标变成了纯色
这是引入小程序内的显示
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200724112330476.png)
这是在iconfont上的图标
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020072411235145.png)
我引用的方法是在iconfont上在线生成css，将里面的css复制直接粘贴到wxss就会出现这样的情况

原来小程序对应font-class不支持多色
|引用方法名	|支持多色	  |兼容性|
|--|--|--|
|unicode| × |  好 |
|font-class|×|良好
|symbol|√|差

mini-program-iconfont-cli(微信小程序解决方案)
把iconfont图标批量转换成多个平台小程序的组件。不依赖字体，支持多色彩。

 1. 在工作的根目录打开cmd，输入

```javascript
npm install mini-program-iconfont-cli --save-dev
```
2.输入命令，生成iconfont.json文件

```javascript
npx iconfont-init
```
3.打开json文件，将`symbol_url`替换成iconfont项目生成的js
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200724113221169.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200724113240218.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70)
4.输入命令生成icon文件夹

```javascript
npx iconfont-wechat
```
5.app.json中引入iconfont组件
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200724113350185.png)
在你需要用到彩色icon的地方（class是自己修改样式）
```javascript
<iconfont name="shuben" class="icon_search"/>
```
就可以显现彩色icon
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200724113528308.png)
如果更新了iconfont项目，就重新生成js文件，替换iconfont.json里面的地址，再执行一次步骤4就可以了
