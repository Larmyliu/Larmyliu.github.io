---
title: JavaScript手写call，apply，bind
date: 2020-07-31 23:46:35
tags: 
	- 前端
	- JavaScript
categories: 前端
---

### JavaScript手写call，apply，bind

call，apply，bind，都能在JavaScript中改变this指向，那么如何用原生的JS来实现呢？

#### call

**实现思路**

1. 参考call的语法规则，需要设置一个参数`thisArg`，也就是this的指向；

2. 将`thisArg`封装为一个Object；

3. 通过为`thisArg`创建一个临时方法，这样`thisArg`就是调用该临时方法的对象了，会将该临时方法的this隐式指向到`thisArg`上；

4. 执行`thisArg`的临时方法，并传递参数；

5. 删除临时方法，返回方法的执行结果。

   ```js
   /*原生实现call*/
   Function.prototype.mycall = function(thisArg, ...arr){
               //先判断合不合法
               if(thisArg === undefined || thisArg === null){
                   //指向全局window
                   thisArg = window;
               }else{
                   //合法创建一个对象
                   thisArg = Object(thisArg);
               }
               //为thisArg创建一个临时方法
               let specialMethod = Symbol('anything');
               //这个this是保存的调用函数
               thisArg[specialMethod] = this;
               let result = thisArg[specialMethod](...arr);
               //删除临时方法
               delete thisArg[specialMethod];
               //返回结果
               return result;
   
           }
           test1 = {
               str:'test1'
           }
           function test2() {
               str = 'test2'
               console.log(this.str);
           }
           test2()//test2
           test2.mycall(test1)//test1
   ```

#### apply

1. apply是第2个参数，这个参数是一个类数组对象：传给`func`参数都写在数组中

```js
Function.prototype.myapply = function(thisArg){
            //先判断合不合法
            if(thisArg === undefined || thisArg === null){
                //指向全局window
                thisArg = window;
            }else{
                //合法创建一个对象
                thisArg = Object(thisArg);
            }
            //为thisArg创建一个临时方法
            let specialMethod = Symbol('anything');
            //这个this是保存的调用函数
            thisArg[specialMethod] = this;
            //apply第二个参数为类数组对象
            function isArrayLike(o){
                if(o && typeof o === "object" && isFinite(o.length) && o.length >= 0 && // o.length为非负值
                o.length === Math.floor(o.length) && // o.length是整数
                o.length < 4294967296){
                return ture;
                }else{
                    return false;
                }
            }
            let args = arguments[1]; // 获取参数数组
            let result;
            if(args){
                if (!Array.isArray(args) && !isArrayLike(args)) {
                    throw new TypeError(
                        "第二个参数既不为数组，也不为类数组对象。抛出错误"
                    );
                } else {
                    args = Array.from(args); // 转为数组
                    result = thisArg[specialMethod](...args); // 执行函数并展开数组，传递函数参数
                }
            }else{
                thisArg[specialMethod]();
            }
            //删除临时方法
            delete thisArg[specialMethod];
            //返回结果
            return result;
        }
        test2()//test2
        test2.myapply(test1);//test1
```

#### bind

##### 实现思路

（1）拷贝调用函数:

- 调用函数，也即调用`myBind`的函数，用一个变量临时储存它；
- 使用`Object.create`复制调用函数的prototype给`funcForBind`；

（2）返回拷贝的函数`funcForBind`；

（3）调用拷贝的函数`funcForBind`：

- `new`调用判断：通过`instanceof`判断函数是否通过`new`调用，来决定绑定的context；
- 通过`call`绑定this、传递参数；
- 返回调用函数的执行结果。

```js
Function.prototype.mybind = function(objThis, ...params){
            const thisFn = this;//存储调用函数
            let funcForBind = function(...secondParams) {
                //检查this是否是funcForBind的实例？也就是检查funcForBind是否通过new调用
                const isNew = this instanceof funcForBind;

                //new调用就绑定到this上,否则就绑定到传入的objThis上
                const thisArg = isNew ? this : Object(objThis);

                //用call执行调用函数，绑定this的指向，并传递参数。返回执行结果
                return thisFn.call(thisArg, ...params, ...secondParams);
            };

            //复制调用函数的prototype给funcForBind
            funcForBind.prototype = Object.create(thisFn.prototype);
            return funcForBind;//返回拷贝的函数 
        }
        let func = function(p,secondParams){//其实测试用的func其参数可以是任意多个
            console.log(p.name);
            console.log(this.name);
            console.log(secondParams);
        }
        let obj={
            name:"1891"
        }
        func.mybind(obj,{name:"coffe"})("二次传参");
        //>> coffe
        //>> 1891
        //>> 二次传参
```

