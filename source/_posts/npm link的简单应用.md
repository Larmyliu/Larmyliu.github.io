# npm link的简单应用

一个项目中，会使用很多依赖，大部分依赖都是开源的第三方依赖，甚至于会有自己**造轮子**开发的依赖在项目中，这时候就会出现一种情况，比如我又一个自己开发的**项目A**，在这个项目中使用了一个自己**造的轮子依赖B**，他们一直相安无事相处了很久，但是有一天我对这个**轮子依赖B**进行了一些功能性的添加，但是我不知道这次的功能增加是否会对我的**项目A**产生不兼容影响，所以我需要测试一下。

那么我们有什么测试方法呢？

### 一种最不推荐的方法

1. 把我们修改后的**轮子依赖B**发布一个beta版本
2. 在我们的项目A中安装这个beta版本
3. 测试

这会有什么问题呢

- 优点：我觉得没有优点
- 缺点：麻烦



### 一种相对可行的方法

1. 在**项目A**中通过相对路径安装**依赖B**
2. 测试

这会有什么问题呢

- 优点：简单（对于不知道npm link的情况下）
- 缺点：如果测试发现有问题，可能多次微调**依赖B**，需要多次`install`

这就好比远程代码，在没有远程代码的时候，我需要每次都要把代码存放到指定仓库，然后换设备后再去仓库拉代码，可以但是也麻烦



### 一种推荐的方法

1. 在项目A中使用软链`npm link 依赖B的路径`
2. 测试

此时修改是直接同步的，就好比可以直接把代码同步到远端，换设备后直接远端同步回来，等功能开发好后，再提交到仓库



### 应用

1. 在同一目录下`path`下创建一个`my-project`和`my-utils`

2. ```
   cd my-project
   npm init -y
   cd ../my-utils
   npm init -y
   ```

3. 在`my-utils`目录下新建一个测试文件`index.js`
   ```js
      function SayHi(){
          console.log("hello,我是工具")
      }
      
      module.exports.SayHi = SayHi
   ```
   
    
   
4. 在`my-project`下建使用`npm link ../my-utils` 
   
4. 写个测试代码
   
   ```js
   import {SayHi} from "my-utils"
   function project(){
       console.log("in project")
       SayHi()
   }
   
   project()
   ```
   
6. 测试

    ![image-20220126152244806](https://larmy-1303020690.cos.ap-guangzhou.myqcloud.com/image-20220126152244806.png)

7. 修改一下`my-utils`再测试

    ![image-20220126152311433](https://larmy-1303020690.cos.ap-guangzhou.myqcloud.com/image-20220126152311433.png)

    

     

       

       

