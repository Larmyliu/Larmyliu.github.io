---
title: node+websocket 简易多人聊天室
date: 2020-08-06 18:08:00
tags:
	- 前端
	- websocket
	- nodeJS
categories: 前端
---

﻿websocket和node一起搭建一个简单聊天室，全部源码已经上传到[github](https://github.com/Larmyliu/chatRoom)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200806171616300.gif)
## 具体实现
#### 环境搭建
创建一个文件夹，建立相对应的css目录，js目录（其他可以先不用管）

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200806172043498.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70)
再根目录下执行命令，安装socket.io

```javascript
npm install socket.io
```
#### 项目搭建
1.构建服务器
在js目录下建立一个`app.js`

```javascript
//app.js
let app = require('http').createServer();
let io = require("socket.io")(app);
let port = 3000
app.listen(port,function(){
    console.log("3000端口已启用")
})
```
2.建立服务端socket连接监听
大家可以看一下[socket.io](https://socket.io/docs/)的文档
（1）socket.emit
客户端与服务器端之间发送消息是用emit
例如客户端向服务端发送登录请求
socket.emit('event',data) event是自定义的事件，后面是带的参数
（2）socket.on
服务器端要接收客户端发送的自定义事件，就得对该事件进行监听
socket.on('event',function(data){})在回调函数中进行处理
同理，服务器端也可以向客户端发送事件，只要客户端也对该事件进行监听就行
（3）io.sockets.emit
服务器端向连接的所有客户端发送消息得用io.sockets.emit
（4）socket.broadcast.emit
给除了自己以外的客户端广播消息
##### 功能（1）：登录
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200806172944915.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70)
**客户端**
获得的用户输入的昵称信息，发送到服务器，触发login事件

```javascript
//index.js
window.onload = function(){
    var socket = io('ws://localhost:3000');
    console.log(socket)
    var nickName = null;
    $('#l_btn').click(function(){
        nickName = $('#login_name').val();
        if(nickName){
            socket.emit('login',{userName:nickName})
        }else{
            alert("请输入用户名")
        }
    })
}
```
**服务器**

```javascript
//app.js
let app = require('http').createServer();
let io = require("socket.io")(app);
let port = 3000
let user = []
io.on('connection', function (socket) {
    /*监听登录*/
    let userName = null;
    socket.on('login',function(data){
        userName = data.userName;
        let index = user.findIndex(e => e==userName)
        if(index === -1){
            user.push(userName);
            socket.emit("loginSuccess",userName);
            io.sockets.emit("add",userName)
        }else{
            socket.emit("loginFail",'')
        }
    })
}
app.listen(port,function(){
    console.log("3000端口已启用")
})
```
登录成功与失败，因为没有连接数据库，所以用user数组来保存，如果数组中没有相同的用户名，则登录成功，并告知所有客户端；如果出现了相同的用户名，则登录失败。

```javascript
//index.js
socket.on("loginSuccess",function(data){
    if(nickName === data){
        $(".chat").show('slow')
        $(".login").hide('slow')
    }
})
socket.on("loginFail",function(data){
    alert("登录失败")
})
socket.on("add",function(data){
    var html = `<div class="office">${data}加入群聊</div>`
    $(".content").append(html);
})
```
##### 功能（2）：发送数据
当点击发送按钮时，告知服务器发送的数据和用户名，服务器告知所有客服端收到的数据

```javascript
//index.js
$(".send").click(function(){
        let message = $("#editor").html();
        console.log(message)
        //清空
        $("#editor").html("");
        if(message){
            socket.emit("sendMessage",{userName:nickName,msg:message})
        }
        var str = `<div class="my">
                    
        <span>${message}</span>
        <span>${nickName}</span>
        </div>`
        $(".content").append(str);
    })
    socket.on("otherMessage", function(data){
        if(data.userName === nickName){
            return
        }
        var str = `<div class="other">
        <span>${data.userName}</span>
        <span>${data.msg}</span>
        </div>`
        $(".content").append(str);
    })
```

```javascript
//app.js
socket.on('sendMessage',function(data){
    console.log(data)
    io.sockets.emit("otherMessage",data)
})
```
##### 功能（3）：退出登录
退出登录告知所有客服端，用户xxx退出了

```javascript
//index.js
socket.on('leave',function(name){
    if(name != null){
        var html = `<div class="office">${name}退出群聊</div>`
        $(".content").append(html);
    }
})
```

```javascript
//app.js
socket.on('disconnect',function(){
   /*向所有连接的客户端广播leave事件*/
   io.sockets.emit('leave',userName)
   user.map(function(val,index){
       if(val === userName){
           user.splice(index,1);
       }
   })
})
```
##### 功能（4）：发送emoji表情
用了jquery的插件[emoji](https://github.com/eshengsky/jQuery-emoji)，![在这里插入图片描述](https://img-blog.csdnimg.cn/20200806174620429.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70)
这两个文件夹是关于emoji的
初始化emoji

```javascript
$("#editor").emoji({
    button: "#btn",
    // showTab: false,
    animation: 'slide',
    position: 'topLeft',
    icons: [{
        name: "QQ表情",
        path: "/dist/img/qq/",
        maxNum: 91,
        excludeNums: [41, 45, 54],
        file: ".gif",
        placeholder: "#qq_{alias}#",
    }],
});
```
在本地是看不到表情包的，可以再根目录的cmd下输入`http-server`到8080端口上看



全部源码已经上传到[github](https://github.com/Larmyliu/chatRoom)
