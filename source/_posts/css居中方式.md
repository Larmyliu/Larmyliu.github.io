html结构比较简单

```html
<body>
    <div class="outer">
        <div class="inner">

        </div>
    </div>
</body>
```

#### 水平居中
1.margin

```css
*{
     padding: 0;
     margin: 0;
 }
 .outer{
     width: 400px;
     height: 400px;
     background-color: red;
     margin: 0 auto;
 }
 .inner{
     width: 200px;
     height: 200px;
     background-color: blue;
     margin: 0 auto;
 }
```
2.text-align+display

```css
.outer{
    width: 400px;
    height: 400px;
    background-color: red;
    text-align: center;
}
.inner{
    display: inline-block;
    width: 200px;
    height: 200px;
    background-color: blue;
}
```
3.定位+位移

```css
.outer{
    width: 400px;
    height: 400px;
    background-color: red;
    position: relative;
    left: 50%;
    transform: translateX(-50%);
}
.inner{
    position: absolute;
    left: 50%;
    transform: translateX(-50%);
    width: 200px;
    height: 200px;
    background-color: blue;
} 
```
4.flex布局

#### 垂直居中
文字
1.单行文字
利用line-height，将其设置为何盒子高度一致

2.多行文字

```css
.outer{
   width: 400px;
    height: 400px;
    background-color: red;
    line-height: 400px;
}
.inner{
    display: inline-block;
    background-color: blue;
    line-height: 20px;  /*单独给子元素设置行高，覆盖父级元素的行高*/
    vertical-align: middle;  
} 
```
盒子
1.定位+位移

```css
.outer{
    width: 400px;
    height: 400px;
    background-color: red;
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
}
.inner{
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
    width: 200px;
    height: 200px;
    background-color: blue;
} 
```
2.flex布局
3.利用margin，前提是父元素设为相对定位

```css
.outer{
    width: 400px;
    height: 400px;
    background-color: red;
    position: relative;
}
.inner{
    position: absolute;
    top: 0;
    left: 0;
    bottom: 0;
    right: 0;
    margin: auto;
    width: 200px;
    height: 200px;
    background-color: blue;
} 
```
4.父元素`display`为table，一般不推荐使用
