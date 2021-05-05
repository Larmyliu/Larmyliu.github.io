今天在用webpack打包文件时报错

```javascript
ERROR in ./src/img/head4.jpg 1:0
Module parse failed: Unexpected character '�' (1:0)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
(Source code omitted for this binary file)
 @ ./src/js/ebtry.js 4:11-38
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200210160034136.png)
提示有不希望出现的错误字符

查阅资料
1.检查自己的file-loader和url-loader是否下载正确

检查下载都正确
看看自己写的webpack.config.js
我在写的时候写成了

```javascript
 {test:/.(png | jpg | gif | svg)$/, use:['url-loader?limit=8192&name=./[name].[ext]']}
```
里面有空格
把空格删除后
再进行`webpack`，先不要用`webpack -w`

成功解决
