关于在node搭建静态服务器的时候，发现无法加载静态的html，直接返回的是`res.end()`里面的内容

```javascript
var server = http.createServer(function (req, res) {
    let pathname = url.parse(req.url).pathname
    let query = changeObj(url.parse(req.url).query)
    console.log(pathname,query);
    if(pathname == "/"){
       res.end("ss")
    }
    else if(pathname == "/require"){
        res.end("/require")
    }
    serve(req, res, finalhandler(req, res))
})
```
原因是在这段代码

```javascript
if(pathname == "/"){
       res.end("ss")
    }
```
因为在地址栏填写`http://localhost:3000`的时候这行代码的条件是`true`的，所以页面会只显示ss，把这段代码注释掉，就可以显示出静态的html默认页面
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200623231226824.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70)
