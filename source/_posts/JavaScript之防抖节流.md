## JavaScript之防抖节流

我记得比软设的时候师兄问有没有使用**防抖函数**或**节流函数**，当时了解但是没用，过了将近一年，有些知识都忘了，痛定思痛，赶紧补上。

![15341407332107](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91c2VyLWdvbGQtY2RuLnhpdHUuaW8vMjAxOC85LzQvMTY1YTI1MmI0YjY5YWFiNQ?x-oss-process=image/format,png)

### 防抖函数（debounce）

在事件被触发n秒后再执行回调，如果在这n秒内又被触发，则重新计时。

这个描述就可以知道他的使用场景：输入框校验，手机号、邮箱验证输入检测，防误触

#### 防抖

一般情况下，我们肯定是和Ajax结合，用于和后端进行数据交互，如果频繁的发起Ajax请求，那么无疑会造成很多资源上的浪费

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>防抖节流</title>
</head>
<body>
    没有防抖的input：<input type="text" id="noDebounce">
    防抖的input: <input type="text" id="Debounce">
    节流的input：<input type="text" id="Throttle">
    <script>
        var noDebounce = document.getElementById("noDebounce");
        var Debounce = document.getElementById("Debounce");
        var Throttle = document.getElementById("Throttle")
        function ajax() {
            console.log(new Date().toLocaleString());
        }
        noDebounce.onkeydown = function() {
            ajax();
        }
    </script>
</body>
</html>
```

![](https://img-blog.csdnimg.cn/20200717000833286.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70)

但是如果使用了防抖函数

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>防抖节流</title>
</head>
<body>
    没有防抖的input：<input type="text" id="noDebounce">
    防抖的input: <input type="text" id="Debounce">
    节流的input：<input type="text" id="Throttle">
    <script>
        var noDebounce = document.getElementById("noDebounce");
        var Debounce = document.getElementById("Debounce");
        var Throttle = document.getElementById("Throttle")
        function ajax() {
            console.log(new Date().toLocaleString());
        }
        noDebounce.onkeydown = function() {
            ajax();
        }
        //防抖函数
        function debounce(fn,delay){
            return function(args){
                clearTimeout(fn.id);
                console.log(args)
                fn.id = setTimeout(function(){
                    //因为是函数，所以this指向window
                    fn.call(this,args);
                },delay)
                
            }
        }
        Debounce.onkeydown = debounce(ajax,1000)
    </script>
</body>
</html>
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200717000856339.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70)

很明显，请求的次数就比没有添加防抖的输入框少，这样就可以节省不少资源。

### 节流函数（Throttle）

在规定的时间内，只触发一次函数。如果在规定时间内触发多次函数，也只执行一次。在搜索框联想，放在重复提交表单等

```js
//节流
        var throttle = function(func, delay) {
            var timer = null;
            var startTime = Date.now();
            return function() {
                    var curTime = Date.now();
                    var remaining = delay - (curTime - startTime);
                    var context = this;
                    var args = arguments;
                    clearTimeout(timer);
                    if (remaining <= 0) {
                        func.apply(context, args);
                        startTime = Date.now();
                    } else {
                        timer = setTimeout(func, remaining);
                    }
            }
        }
        Throttle.onclick = throttle(ajax,3000)
```


