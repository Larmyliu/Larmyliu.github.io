最近做的h5项目中有一个微信登录功能，将自己遇到的一些坑记录一下

前提：

 1. 一个公众号
 2. appid
 
 首先就是在自己的vue项目中引入微信的接口
 `<script src="http://res.wx.qq.com/connect/zh_CN/htmledition/js/wxLogin.js"></script>`
 
 接着我们就可以在自己的login.vue中调用微信函数`WxLogin`
 
在login.vue中的mounted

```javascript
var obj = new WxLogin({
                id:"div",    //div的id
                appid: "公众号里的appid",
                scope: "snsapi_login",
				redirect_uri: "http%3a%2f%2f96ac7d.natappfree.cc%2f%23%2fjump",//urlencode编码
        });
```
解释一下redirect_url:

 1. 如果是在开发环境，也就是用8080端口，网站还没有域名时，但是需要外网穿透，这时候你需要下一个natapp，可以帮你改自己的域名。（natapp不要下在c盘，我的下在c盘打不开，换到d盘就打开了，natapp的配置官网讲的很清楚）
 2. 要用**urlencode编码**把redirect_url转化一下，这个百度转码工具就可以了
 3. **域名有了要修改微信开放平台里的授权回调域**，这样才不会报参数错误
 
现在你就可以扫码登录了，空白页作为跳转中转站jump.vue，也就是上面的重定向地址，在这个地址里可以就收到code。code作为参数向后端发一次axios请求，得到用户信息
