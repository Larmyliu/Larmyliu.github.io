需要一个`serve-static`模块，可以用npm或者cnpm下载
1.使用express模块框架

```javascript
var express = require('express')
var serveStatic = require('serve-static')
var url = require("url")
var app = express()
app.use(serveStatic(__dirname, { 'index': ['demo.html', 'demo.htm'] }))
//放图片资源
// app.use(serveStatic(__dirname+"/img"))
app.get('/',function(req,res){
  console.log('url', url.parse(req.url));
  res.send("success")
  
	
})
app.get('/require',function(req,res){
  console.log('url', url.parse(req.url));
  let query =changeObj(url.parse(req.url).query);
  console.log(query)
 res.send("success")
  
})
function changeObj(query){
  if(query == null || query == ""){
    return {};
  }
  let arr = query.split("&");
  let obj = {};
  for(let i = 0; i < arr.length; i++){
    let temp = arr[i].split("=")
    obj[temp[0]] = temp[1]
  }
  return obj;
}
app.listen(3000,function(){
  console.log("3000端口已启用")
})


```
2.使用传统的http模块

```javascript
var http = require("http")
var url = require("url")
var serveStatic = require('serve-static')
var finalhandler = require('finalhandler')
var serve = serveStatic(__dirname, { 'index': ['demo.html', 'demo.htm'] })
var server = http.createServer(function (req, res) {
    let pathname = url.parse(req.url).pathname
    let query = changeObj(url.parse(req.url).query)
    console.log(pathname,query);
    
    if(pathname == "/require"){
        res.end("/require")
    }
    serve(req, res, finalhandler(req, res))
})
function changeObj(query){
    if(query == null || query == ""){
      return {};
    }
    let arr = query.split("&");
    let obj = {};
    for(let i = 0; i < arr.length; i++){
      let temp = arr[i].split("=")
      obj[temp[0]] = temp[1]
    }
    return obj;
  }
server.listen(3000,function(){
    console.log("3000端口已启动")
})
```


再解决一下node跨域
1.可以使用`cors模块`，也是需要npm或者cnpm下载
在调用接口前写上

```javascript
 var cors = require("cors")
 app.use(cors())
```
2.配置请求头

```javascript
var originList = ['http://localhost:3000','http://localhost:30001']
app.all("*",function(req,res,next){
    //允许所有域名跨域，不安全
    // res.header("Access-Control-Allow-Origin","*");
    //只允许3000端口
    // res.header("Access-Control-Allow-Origin","http://localhost:3000");
    //设置多个端口
    if(originList.includes(req.headers.origin.toLowerCase())){
        //设置允许跨域的域名，*代表允许任意域名跨域
        res.header("Access-Control-Allow-Origin",req.headers.origin);
    }
    res.header("Access-Control-Allow-Headers", "Content-Type,Content-Length, Authorization, Accept,X-Requested-With");
    res.header("Access-Control-Allow-Methods","DELETE,PUT,POST,GET,OPTIONS");
    if (req.method.toLowerCase() == 'options')
        res.send(200);
    else
     
```
完整代码

```javascript
var express = require('express')
var serveStatic = require('serve-static')
var url = require("url")
var app = express();
// var cors = require("cors")
// app.use(cors())
var originList = ['http://localhost:3000','http://localhost:30001']
app.all("*",function(req,res,next){
    //允许所有域名跨域，不安全
    // res.header("Access-Control-Allow-Origin","*");
    //只允许3000端口
    // res.header("Access-Control-Allow-Origin","http://localhost:3000");
    //设置多个端口
    if(originList.includes(req.headers.origin.toLowerCase())){
        //设置允许跨域的域名，*代表允许任意域名跨域
        res.header("Access-Control-Allow-Origin",req.headers.origin);
    }
    res.header("Access-Control-Allow-Headers", "Content-Type,Content-Length, Authorization, Accept,X-Requested-With");
    res.header("Access-Control-Allow-Methods","DELETE,PUT,POST,GET,OPTIONS");
    if (req.method.toLowerCase() == 'options')
        res.send(200);
    else
        next();
})
app.get('/cors',function(req,res){
    console.log('url', url.parse(req.url));
    // res.send("成功收到5478端口消息")
    res.end("成功收到5479端口消息")

})
app.listen(5479,function(){
    console.log("5479端口已启用")
  })
  
```

