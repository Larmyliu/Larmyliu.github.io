最近学了一下koa2和MongoDB，写个注册功能更加熟悉一下

首先，用`vue+vant`把注册页面写出来

1.Vue方面都是一些很常用的知识点，`vant`是一个比较适用于移动端的UI组件库，而`element-ui`更加适合h5网页版

register.vue
```html
<template>
    <div>
        <van-nav-bar title="用户注册" left-text="返回"  left-arrow @click-left="goBack" />
        <div class="register-panel">
            <van-field type="text" label="用户名" placeholder="请输入用户名" v-model="username" icon="clear" required @click-icon="username = ''"/>
            <van-field type="password" label="密码" placeholder="请输入密码" v-model="password" required/>
            <div class="register-button">
                <van-button type="primary" size="large" @click="axiosRegisterUser">马上注册</van-button>
            </div>
        </div>
    </div>
</template>

<script>
import axios from "axios";
import url from "@/serviceAPI.config.js"
export default {
    data() {
        return {
            username: '',
            password: '',
        }
    },
    methods: {
        goBack() {
            this.$router.go(-1)   
        },
        axiosRegisterUser() {
            axios({
                url: url.registerUser,
                method:"post",
                data:{
                    username:this.username,
                    password:this.password
                }
            })
            .then(response => {
                console.log(response)
            })
            .catch(err => {
                console.log(err)
            })
        }
    },
}
</script>

<style scoped>
    .register-panel{
        width: 96%;
        border-radius: 5px;
        margin: 20px auto;
        padding-bottom: 50px;
    }
    .register-button{
        padding-top: 10px;
    }
</style>
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201006101655383.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70#pic_center)
有了用户界面，我们就应该把数据库弄出来，这里用的是MongoDB，相比于MySQL而言，一个是非关系性的数据库，一个是关系型数据库。具体差别可以看看别人的博客。MongoDB的存储形式是键值对，和我们平时写的JS很像，所以上手还是非常方便的。

1.首先安装`MongoDB`，[官方网站](https://www.mongodb.com/)
2.配置环境变量
3.安装盘的根目录，建立data/db,这两个文件夹
可以看看人家的安装博客

如果安装成功的话，打开cmd运行`mongod`和`mongo`
出现这个界面可以了
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201006103150547.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70#pic_center)
当然也可以用MongoDB的图形界面，比较美观。
我用了另一个叫`Robo3`的，[官方链接](https://robomongo.org/download)
是长这个样子的
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201006103350417.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70#pic_center)
安装完成后，就要开始我们编写后台的代码了

首先就是下载`koa`框架

```javascript
npm install --save koa
```
可以先写一个简单的demo测试一下成不成功

index.js
```javascript
const Koa = require('koa')
const app = new Koa()

app.use(async(ctx)=>{
    ctx.body = '<h1>hello Koa2</h1>'
})

app.listen(3000,()=>{
    console.log('3000端口已启用')
})
```
`node index.js`在浏览器中打开`127.0.1:3000`出现`hello Koa2`就是成功的

接下来是要把数据库连上

Mongoose是一个开源的封装好的实现Node和MongoDB数据通讯的数据建模库

```javascript
npm install mongoose --save
```

##### Schema，相当于MongoDB数据库的一个映射

 - Schema中的数据类型
 - String ：字符串类型 
 - Number ：数字类
 - Date ： 日期类型 
 - Boolean： 布尔类型
 - Buffer ： NodeJS buffer 类型 
 - ObjectID ： 主键,一种特殊而且非常重要的类型
 - Mixed ：混合类型
 - Array ：集合类型
  
##### 定义一个用户Schema

```javascript
const mongoose = require("mongoose");
//bcrypt是加盐加密要用到的模块，会放到最后再说
const bcrypt = require("bcrypt")
const Schema = mongoose.Schema;
let ObjectId = Schema.Types.ObjectId;
const SALT_WORK_FACTOR = 10
// 创建Schema
const userSchema = new Schema({
    UserId: ObjectId,
    userName: {
        unique: true,
        type: String
    },
    password: String,
    createAt: {
        type: Date,
        default: Date.now()
    },
    lastLoginAt: {
        type:Date,
        default: Date.now()
    }
})
// 发布模型
mongoose.model("User", userSchema)
```
载入所有的Schema

安装glob

`npm install glob --save`

完成数据库的初始化

```javascript
const mongoose = require("mongoose");
const db = "mongodb://localhost/simle-db"
const glob = require("glob");
const {resolve} = require("path")
mongoose.Promise = global.Promise;
exports.initSchemas = () => {
    glob.sync(resolve(__dirname, "./schema/", "**/*.js")).forEach(require);
}
exports.connect = () => {
  //连接数据库
  mongoose.connect(db);
  let maxConnectTimes = 0;
  return new Promise((resolve, reject) => {
    //失去连接时重新连接
    mongoose.connection.on('disconnected',()=>{
        console.log('***********数据库断开***********')
        if(maxConnectTimes<3){
            maxConnectTimes++
            mongoose.connect(db)    
        }else{
            reject()
            throw new Error('数据库出现问题，程序无法搞定，请人为修理......')
        }

    })
    //发生错误时候，打印错误，再重连
    mongoose.connection.on('error',err=>{
        console.log('***********数据库错误***********')
        if(maxConnectTimes<3){
            maxConnectTimes++
            mongoose.connect(db)   
        }else{
            reject(err)
            throw new Error('数据库出现问题，程序无法搞定，请人为修理......')
        }

    })
    //连接打开时
    mongoose.connection.once('open',()=>{
        console.log('MongoDB connected successfully') 
        resolve()   
    })
  })

}

```
#### bcrypt
bcrypt是一种跨平台的文件加密工具。bcrypt 使用的是布鲁斯·施内尔在1993年发布的 Blowfish 加密算法。由它加密的文件可在所有支持的操作系统和处理器上进行转移。它的口令必须是8至56个字符，并将在内部被转化为448位的密钥。

在安装bcrypt模块前要先安装node-gyp

```javascript
npm install --save node-gyp
```
再安装bcrypt模块

```javascript
npm instal --save bcrypt
```
用pre每次进行保存时都进行加盐加密的操作
```javascript
userSchema.pre('save', function(next){
    //let user = this
    console.log(this)
    bcrypt.genSalt( SALT_WORK_FACTOR,(err,salt)=>{
        if(err) return next(err)
        bcrypt.hash(this.password,salt, (err,hash)=>{
            if(err) return next(err)
            this.password = hash
            next()
        }) 

    })
})
```
完整的代码

```javascript
const mongoose = require("mongoose");
const bcrypt = require("bcrypt")
const Schema = mongoose.Schema;
let ObjectId = Schema.Types.ObjectId;
const SALT_WORK_FACTOR = 10
// 创建Schema
const userSchema = new Schema({
    UserId: ObjectId,
    userName: {
        unique: true,
        type: String
    },
    password: String,
    createAt: {
        type: Date,
        default: Date.now()
    },
    lastLoginAt: {
        type:Date,
        default: Date.now()
    }
})
userSchema.pre("save", function(next){
    console.log(this);
    bcrypt.genSalt( SALT_WORK_FACTOR, (err, salt) => {
        if(err){
            return next(err);
        }
        bcrypt.hash(this.password, salt, (err, hash) => {
            if(err){
                return next(err)
            }
            this.password = hash
            next();
        })
    })
})
// 发布模型
mongoose.model("User", userSchema)
```

#### 路由模块化
安装`koa-router`

```javascript
npm install --save koa-router 
```
新建一个`api`文件夹，专门存放接口文件

接口文件user.js

```javascript
const Router = require ('koa-router')
let router = new Router()
router.get('/',async(ctx)=>{
    ctx.body="这是用户操作首页"
})
router.get('/register',async(ctx)=>{
    ctx.body="用户注册接口"
})
module.exports=router;
```

在index.js的文件顶部，引入koa-router 

```javascript
//使用koa框架
const Koa = require('koa')
const app = new Koa()
//数据库模型，初始化
const mongoose = require("mongoose");
const {connect, initSchemas} = require("./database/init")
//路由模块化
const Router = require("koa-router");
let user = require("./appApi/User.js");
const { allowedMethods } = require('./appApi/User.js');


// 加载所有子路由
let router = new Router();
router.use("/user", user.routes())

// 加载中间件
app.use(router.routes());
app.use(router.allowedMethods())
```
可以在`http://localhost:3000/user`看看有没有成功

打通前后端通信

安装koa-bodyparser中间件

```javascript
npm install --save koa-bodyparser
```
在index.js中引入

```javascript
const bodyParser = require('koa-bodyparser')
app.use(bodyParser());
```

配置一下我们的接口地址文件`serviceAPI.config.js`

```javascript
const BASEURL = "https://www.easy-mock.com/mock/5f789886e30a0d4abc61798a/smileVue/";
const LOCALURL = "http://localhost:3000/"
const URL = {
    getShoppingMallInfo:BASEURL+'index',
    getGoodsInfo:BASEURL+'getGoodsInfo',
    registerUser:LOCALURL+"user/register"
}

module.exports = URL
```

前后端服务都开启，就可以看到，已经成功收到我们的用户名和密码
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020100611005153.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70#pic_center)
但还没有写进数据库里，所以还需要再完善一下
在User.js中

```javascript
router.post("/register", async(ctx) => {
    const User = mongoose.model("User");
    let newUser = new User(ctx.request.body);
    await newUser.save().then(() => {
        ctx.body = {
            code: 200,
            message: "注册成功"
        }
    }).catch(err => {
        ctx.body = {
            code: 500,
            message: err
        }
    })
})
```
在注册页面

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201006111935654.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70#pic_center)
更新数据库后可以看到我们的用户名和密码已经被记录了
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201006111742714.png#pic_center)
























