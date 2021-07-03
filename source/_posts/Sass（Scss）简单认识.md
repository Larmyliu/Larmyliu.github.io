## Sass（Scss）简单认识

我个人平时比较常使用的是Less，但是公司前端开发是在Vue中用Sass，为了统一一下语言，所以学习一下Sass。

#### 简单介绍

Sass和Less一样是Css的预处理器，相比于Css需要重复写大量重复的样式，Sass和Less的好处可以节省大量重复的代码操作，可以通过嵌套样式代码，父子节点之间的关系更加明确，同时还有引入变量，循环，函数等

#### Sass Or Scss

其实在官网显示的是sass，但是我们在vue中通常写的是`lang = 'scss'`,其实两个比没有太大的区别，简言之可以理解scss是sass的一个升级版本，完全兼容sass之前的功能，又有了些新增能力。语法形式上有些许不同，最主要的就是sass是靠缩进表示嵌套关系，scss是花括号。

#### 联系平台

https://www.sassmeister.com/（可以在线将sass转换成css）

#### 基本用法

##### 变量

变量声明用`$`，优先使用的是局部变量

```scss
 $n: 10;
 p{
    $i: 100px;
    $n: 1000px;
    width: $n px;
    height: $i px;
 }
```

```css
p {
  width: 1000px px;
  height: 100px px;
}
```

如果需要镶嵌在字符串中需要用到`#{}`包裹起来

```scss
scss $n: left;
 p{
    $i: 100px;
    padding-#{$n}: 100px;
    height: $i px;
 }
```

```css
p {
  padding-left: 100px;
  height: 100px px;
}
```

##### 计算

相比于`calc`功能更加强大

```scss
 $n: left;
 p{
    $i: 100px;
    padding-#{$n}: 100px;
    height: $i px;
    width: (300 - 100)px;
    margin: (14/2) px;
    padding: 100 * $i;
 }
```

```css
p {
  padding-left: 100px;
  height: 100px px;
  width: 200 px;
  margin: 7 px;
  padding: 10000px;
}
```

##### 嵌套

可以根据父子节点的顺序进行书写嵌套，可以使用`&`表示伪类

```scss
 $n: left;
 div{
    $i: 100px;
    padding-#{$n}: 100px;
    height: $i px;
    width: (300 - 100)px;
    margin: (14/2) px;
    padding: 100 * $i;
    p{
        width:100px;
    }
    a{
        color:red;
        &:hover{
            color:blue;
        }
    }
 }
 
```

```css
div {
  padding-left: 100px;
  height: 100px px;
  width: 200 px;
  margin: 7 px;
  padding: 10000px;
}
div p {
  width: 100px;
}
div a {
  color: red;
}
div a:hover {
  color: blue;
}
```

##### 继承

可以继承样式，根据需求进行覆盖修改

```scss
 $n: left;
 div{
    $i: 100px;
    padding-#{$n}: 100px;
    height: $i px;
    width: (300 - 100)px;
    margin: (14/2) px;
    padding: 100 * $i;
    background:red;
    p{
        width:100px;
    }
    a{
        color:red;
        &:hover{
            color:blue;
        }
    }
 }
 .testDiv{
     @extend div;
     width: 1000px;
     background: blue;
 }
```

```css
div, .testDiv {
  padding-left: 100px;
  height: 100px px;
  width: 200 px;
  margin: 7 px;
  padding: 10000px;
  background: red;
}
div p, .testDiv p {
  width: 100px;
}
div a, .testDiv a {
  color: red;
}
div a:hover, .testDiv a:hover {
  color: blue;
}

.testDiv {
  width: 1000px;
  background: blue;
}
```

##### Mixin

Mixin是可重复用的代码块，通过`@mixin`来注册，可以通过`@include`来调用，可以设置参数和缺省值

```scss
@mixin test($value: 10){
     float: left;
     width: $value px;
 }
 @mixin test2($value: 10){
     height: $value px;
 }
 div{
     @include test();
     @include test2(100);
 }
```

```css
div {
  float: left;
  width: 10 px;
  height: 100 px;
}
```

##### 条件语句

可以使用`@if`进行条件判断

```scss
 p{
     @if (1 + 1 == 2) {
         font-size: 20px;
     }
     @if (5 < 3) {
         font-size:30px;
     }
 }
```

```css
p {
  font-size: 20px;
}
```

##### 循环语句

有for循环，while循环和each循环（类似js中的for...in）

```scss
$i: 0;
$n: 0;
@for $i from 1 to 5{
    p:nth-child(#{$i}){ // 当作参数传递的时候需要用到#{}
        color: red;
    }  
}
@while $n < 10{
    div:nth-child(#{$n}){
        color: blue;
    } 
    $n: $n + 1
}
@each $member in a, b, c, d {
    .#{$member} {
        background-image: url("/image/#{$member}.jpg");
    }
}
```

```css
p:nth-child(1) {
  color: red;
}

p:nth-child(2) {
  color: red;
}

p:nth-child(3) {
  color: red;
}

p:nth-child(4) {
  color: red;
}

div:nth-child(0) {
  color: blue;
}

div:nth-child(1) {
  color: blue;
}

div:nth-child(2) {
  color: blue;
}

div:nth-child(3) {
  color: blue;
}

div:nth-child(4) {
  color: blue;
}

div:nth-child(5) {
  color: blue;
}

div:nth-child(6) {
  color: blue;
}

div:nth-child(7) {
  color: blue;
}

div:nth-child(8) {
  color: blue;
}

div:nth-child(9) {
  color: blue;
}

.a {
  background-image: url("/image/a.jpg");
}

.b {
  background-image: url("/image/b.jpg");
}

.c {
  background-image: url("/image/c.jpg");
}

.d {
  background-image: url("/image/d.jpg");
}
```

##### 自定义函数

可以编写自己的函数

```scss
@function myFunction($value){
    @return $value * 2;
}
div{
    width: myFunction(100)px;
}
```

```css
div {
  width: 200 px;
}
```

