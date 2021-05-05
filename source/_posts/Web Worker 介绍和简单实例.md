JavaScript是单线程语言，前面的任务没做完，后面的任务只能等着。
web worker可以为JavaScript创建多线程环境，好处是，一些计算密集型或高延迟的任务，被 Worker 线程负担了，主线程（通常负责 UI 交互）就会很流畅，不会被阻塞或拖慢。

Web Worker 有以下几个使用注意点。

（1）同源限制

分配给 Worker 线程运行的脚本文件，必须与主线程的脚本文件同源。

（2）DOM 限制

Worker 线程所在的全局对象，与主线程不一样，无法读取主线程所在网页的 DOM 对象，也无法使用document、window、parent这些对象。但是，Worker 线程可以navigator对象和location对象。

（3）通信联系

Worker 线程和主线程不在同一个上下文环境，它们不能直接通信，必须通过消息完成。

（4）脚本限制

Worker 线程不能执行alert()方法和confirm()方法，但可以使用 XMLHttpRequest 对象发出 AJAX 请求。

（5）文件限制

Worker 线程无法读取本地文件，即不能打开本机的文件系统（file://），它所加载的脚本，必须来自网络。

具体的一些函数和使用方法看看阮老师的博客
[阮一峰web worker](http://www.ruanyifeng.com/blog/2018/07/web-worker.html)

下面是我用web worker写的一个小demo，最近绿茶挺火的，我叫他“茶艺聊天室”
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200725234345405.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70)
html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <link rel="stylesheet" href="css/communication.css">
</head>
<body>
    <div class="communicated">
        <!-- <i class="return" id="back"></i> -->
        
        <div class="bigBox">
            <div class="connent">
                <div class="contant" id="window-talk">
                </div>
                <div class="send">
                        <form action="/upload/img" method="post" enctype="multipart/form-data" name="photo" id="selectPro" target="nm_iframe" multiple="multiple">
                            <input type="file" name="photo" id="inputphoto" value=""  multiple="multiple">
                           
                        </form>
                    <div name="sent" id="msg1" class="seatArea" placeholder="请输入发送内容"  contenteditable="true" style="overflow: auto;"></div>
                    <button class="stuSend" id="btn">发送</button>
                </div>
            </div>
        </div>



    </div>
    <script type="text/javascript" src="./talk.js"></script>
</body>
</html>

```
css

```css
*{
    padding: 0;
    margin: 0;
    list-style: none;
    text-decoration: none;
}
html{
    background: #eee5e5;
}
.communicated{
    width: 100%;
    height: 100%;
}
.bigBox{
    position: relative;
    margin: 0 auto;
    width: 1260px;
    height: auto;
    /* background-image: url(../img/peo-left.png),url(../img/peo.png); */
    background-size: 15%;
    background-position: 142px 246px,931px 227px;
    background-repeat: no-repeat,no-repeat;
}
.return{
    position: absolute;
    top: 0px;
    left: 20px;
    width: 100px;
    height: 100px;
    background-image: url(../img/fanhui.png);
    background-size: contain;
    background-repeat: no-repeat;
}
.connent{
    /* border: 1px solid #000; */
    margin: 50px auto;
    width: 600px;
    height: 500px;
    border-radius: 10px;
    /* background: #fff; */
}
.contant{
    border: 1px solid #888;
    position: relative;
    width: 600px;
    height: 350px;
    overflow: auto;
    border-radius: 10px 10px 0 0;
    background: #fff;
}
.send{
    position: relative;
    width: 600px;
    height: 150px;
    border: 1px solid #888;
    background: #fff;
    border-top: none;
    border-radius: 0 0 5px 5px;
}
.seatArea{
    margin: 0px;
    width: 579px;
    border: none;
    height: 110px;
    resize: none;
    font-size: 18px;
    padding: 10px;
}
.stuSend{
    position: absolute;
    right: 10px;
    bottom: 6px;
    border: none;
    border-radius: 10px;
    width: 70px;
    height: 30px;
    text-align: center;
    color: #333;
    cursor: pointer;
}
.stuSend:focus{
    outline: none;
}
.own{
    float: right;
    overflow: auto;
    border-radius: 5px;
    padding: 10px;
    margin: 10px 0;
    background: #7fff00;
    max-width: 300px;
    height: auto;
    margin: 10px 0;
}
.bg-green{

    background: #9eea6a;
}
.bg-white{

    background: #eee;
}
.other{
    float: left;
    overflow: auto;
    border-radius: 5px;
    padding: 10px;
    max-width: 300px;
    height: auto;
    margin: 10px;
}
.line{
    float: left;
    width: 580px;
}
.seatArea:focus{
    outline: none;
    border: none;
}
.stuSend:hover{
    color: #fff;
    background: #8e0b02;
}
#data-list {

    position: absolute;
    right: 80px;
    bottom: 6px;
    border: none;
    border-radius: 10px;
    width: 116px;
    height: 30px;
    text-align: center;
    color:
            #333;
    cursor: pointer;
    border: 1px black;
    display: block;


}
#data-list>*{
    width: 98px;
    height: 21px;
}
#get-page{
    width: 300px;
    height: 100px;
    display: block;
    margin: auto;
}
.history{
    display: block;
    text-align: center;
    font-size: 14px;
    color:#6b9bdc;
}
.loadding{
    /* display: none; */
    margin: 0 auto;
    width: 20px;
}

.photo{
    width: 30px;
    height: 30px;
    position: absolute;
    bottom: 110px;
    right: 10px;
    cursor: pointer;
}
#inputphoto{
    position: absolute;
    bottom: 97px;
    right: 5px;
    opacity: 0;
    /* left: 140px; */
    width: 40px;
    height: 40px;
    z-index: 3;
    cursor: pointer;
}
.seatArea:empty:before{
    content: attr(placeholder);   /* element attribute*/
    /*content: 'this is content';*/
    color: #888;
}
/*焦点时内容为空*/
.seatArea:focus:before{
    content:none;
}
.seatArea img{
    height: 100px;
}
```
js

```javascript
//talk.js
var msg1 = document.getElementById("msg1");
msg1.addEventListener("keydown",function(e) {
    if(e.keyCode === 13){
        order()
    }
})


if(typeof(Worker)!=="undefined"){
    var myWorker = new Worker("my_task.js");
    myWorker.onmessage = function (oEvent) {//接收线程发过来的消息
      console.log("Worker said : " + oEvent.data);
    };
    document.getElementById("btn").onclick=order
}else{
    alert("不支持web worker ");
}
function order() {
    var msg= document.getElementById("msg1").innerHTML;
        document.getElementById("msg1").innerHTML = " "
        myWorker.postMessage(msg);//向另外的线程发送消息
        var div=document.createElement("div");
        div.className = "line";
        var div2 = document.createElement('div');
        div2.innerHTML= msg;
        div2.className = "bg-green own"
        div.appendChild(div2)
        document.getElementById("window-talk").appendChild(div);
        myWorker.onmessage=function(data){
            var div_inner=document.createElement("div");
            div_inner.className = "line";
            var div2_inner = document.createElement('div');
            div2_inner.innerHTML= msg;
            div2_inner.className = "bg-white other"
            div_inner.appendChild(div2_inner)
            div2_inner.innerHTML=data.data.msg;
            div_inner.appendChild(div2_inner);
            document.getElementById("window-talk").appendChild(div_inner);
        }
}
```

```javascript
//my_task.js
var send = ['你好骚啊', '你要这样想我也没办法','滚']
var i = 0;
onmessage = function (oEvent) {//对主线程回传回来的消息进行处理

    postMessage( {
        time:new Date(),
        msg:send[i]
    });
    i == 2 ? i = 0 : i++;
};
  
```

