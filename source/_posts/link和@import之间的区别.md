link和@import都可以导入css样式，但是他们之间有什么区别？
区别：
1.link不仅可以导入样式表，还可以导入其他如如favicon，但@import只能导入样式表
2.link是XHTML语法，不存在兼容问题，但是@import是在css2.1才提出，在IE5+后才支持，所以对低版本的浏览器不兼容
3.可以通过 JS 操作 DOM ，插入link标签来改变样式；由于 DOM 方法是基于文档的，无法使用@import的方式插入样式。
4.link是在页面加载的的同时一起加载的，@import是页面加载完毕后才加载的
5.link引入的样式权重大于@import引入的样式（**有争议**）

关于5，link的权重比@import的大，说法应该是不正确的（如果有其他看法欢迎评论留言）。
我写了个很简单的demo

```html
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" href="./css/link样式表.css">
    <style>
        @import url(./css/@import的样式表.css);
    </style>
    
</head>
<body>
    <div class="outer">
        <div class="inner">
            <p style="color: #fff;">我是行内样式</p>
            <p>我是最普通的p标签</p>
            <p>我是加了选择器的p标签</p>
        </div>
    </div>
</body>
</html>
```
@import的样式表
```css
.outer{
    background: blue;
}
.inner{
    background: red;
}
```
link样式表
```css
*{
    padding: 0;
    margin: 0;
    color: #000 !important;
}
.outer{
    width: 200px;
    height: 200px;
    background: red;
    margin: 0 auto;
}
.inner{
    width: 100px;
    height: 100px;
    background: blue;
    margin: 0 auto;
}
.inner>p{
    color: #666;
}
```
当把link放在import之前
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200928152924724.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70#pic_center)
当把link放在@import后
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020092815303953.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70#pic_center)
放的位置不一样导致了两个div的颜色发生改变，相同权重下，在后面写的同名样式覆盖掉了前面写的同名样式，所以并没有存在link的权重会比@import的大
