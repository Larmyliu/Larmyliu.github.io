在JavaScript中，数组使用sort()后发现有排序不正确的情况
排序前
`var a = [10,6,6,10,10,9,8,8,3,3,8,2,1,5,1,9,5,2,7,4,7,7]`
排序后
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200612184236415.png)
很明显排序不正确
查阅资料后，数组元素首先会被转换为字符，例如
`1变成"1"`,之后会根据`Unicode`编码的顺序来进行排序，所以这就是为什么10排到了2前面
解决方法是，指定好`compareFunction`

```javascript
a.sort(function (a,b) {
            if (a < b ) {    
                return -1;
            }
            if (a > b ) {
                return 1;
            }
            // a must be equal to b
            return 0;
        });
```
这样排序后就没有问题了
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200612184712374.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70)



