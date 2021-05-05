**VUE报错Uncaught TypeError: __WEBPACK_IMPORTED_MODULE_0_jquery___default(...)(...).niceScroll is not a function**

在vue项目里报了这个错，原因是我想在这个地方初始化时，利用一下一下jq的美化滚动条

报错原因
我在mounted里面写的美化滚动条
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200512111252400.png)
解决方法
应该在外部js写好，在vue中引用
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200512111408705.png)
