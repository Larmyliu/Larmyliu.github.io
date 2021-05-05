用vue写了一个h5移动端页面，因为涉及到了很多动图所以用了腾讯云的CDN加速，但是出现了一个神奇的问题，图片类都可以使用CDN地址，但是字体文件浏览器却报了跨域错误

原因是：字体文件在CDN服务器上、项目部署在后端的服务器

然后
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200929195427816.png#pic_center)
解决方法是：

将字体文件转成base64，这个[网站](https://transfonter.org/)可以转换 
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200929195603934.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70#pic_center)
记得要把这个勾上

然后把里面的css，copy到自己的项目要用到的地方，就可以愉快的显示了







