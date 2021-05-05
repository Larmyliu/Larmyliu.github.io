## JavaScript之深浅拷贝

了解一下JavaScript中的深浅拷贝，以及他们的手写版

### 数据类型

首先肯定要知道的是JavaScript的数据类型，JavaScript的数据类型可以分为两大类**基本数据类型**，**引用数据类**。

#### 基本数据类型

- Number
- Boolean
- String
- Null
- Undefined
- Symbol
- Bigint

#### 引用数据类型

- Function
- Object
- Array

了解完JavaScript拥有的数据类型后，来看看深浅拷贝

### 浅拷贝

简单的来说就是，基本数据类型完全拷贝他们的数据，引用数据类型就拷贝内存地址

#### 实现

1. `Object.assign()`

   ```js
   var obj = {
   	name: 'ljm',
       age: 18,
       like:{
   		first: "sleep"
       }
   }
   var obj1 = Object.assign({},obj)
   console.log(obj1.name)//ljm
   console.log(obj1.age)//18
   obj1.age = 20
   obj1.like.first = "reading"
   console.log(obj.age)//18
   console.log(obj1.age)//20
   console.log(obj.like.first)//reading
   console.log(obj1.like.first)//reading
   ```

   这个是Object中的一个内置函数，可以对Object对象中的数据进行浅复制，name和age是obj对象中的基本数据类型，所以浅复制后obj1改变数据不会改变obj的数据，但是like是引用数据类型，只是复制了地址，所以obj2中like改变属性值也会改变obj中like的属性值。

2. 拓展运算符`...`

   ```js
   var obj = {
   	name: 'ljm',
       age: 18,
       like:{
   		first: "sleep"
       }
   }
   var obj1 = {...obj}
   console.log(obj1.name)//ljm
   console.log(obj1.age)//18
   obj1.age = 20
   obj1.like.first = "reading"
   console.log(obj.age)//18
   console.log(obj1.age)//20
   console.log(obj.like.first)//reading
   console.log(obj1.like.first)//reading
   ```

   拓展运算符是es6中的语法糖，在Object或Array中都可以使用。

3. `Array.prototype.slice`

   ```js
   var arr1 = ["ljm", 18, ["sleep"]];
   var arr2 = arr1.slice(0);
   arr2[1] = 20;
   arr2[2][1] = "reading";
   console.log(arr1[1])//18;
   console.log(arr2[1]);//20
   console.log(arr1[2][1])//reading
   console.log(arr2[2][1])//reading
   ```

4. `Array.prototype.concat`

   ```js
   var arr1 = ["ljm", 18, ["sleep"]];
   var arr2 = [].concat(arr1)
   arr2[1] = 20;
   arr2[2][1] = "reading";
   console.log(arr1[1])//18;
   console.log(arr2[1]);//20
   console.log(arr1[2][1])//reading
   console.log(arr2[2][1])//reading
   ```

### 深拷贝

深拷贝就是不管是基本数据类型还是引用数据类型都重新拷贝一份， 不存在共用数据的现象

#### 实现

1. `JSON.parse(JSON.stringify(obj))`

   ```js
   var obj = {
   	name: 'ljm',
       age: 18,
       like:{
   		first: "sleep"
       }
   }
   var obj1 = JSON.parse(JSON.stringify(obj));
   console.log(obj1.name)//ljm
   console.log(obj1.age)//18
   obj1.age = 20
   obj1.like.first = "reading"
   console.log(obj.age)//18
   console.log(obj1.age)//20
   console.log(obj.like.first)//sleep
   console.log(obj1.like.first)//reading
   ```

   存在的问题

   1. 会忽略undefined
   2. 会忽略symbol
   3. 不能序列化函数
   4. 不能解决循环引用的对象
   5. 不能正确处理`new Date()`
   6. 不能处理正则
   7. 不能处理new Error()

   现实中遇到深拷贝的场景很少，而且99%用JSON.stringify可以解决

2. 递归处理

   ```js
   function copyFunction(func) {
   	let fnStr = func.toString()
   	return func.prototype ? eval(`(${fnStr})`) : eval(fnStr)
   }
   
   function deepCopy (obj, cache = []) {
       if (typeof obj === 'function') {
           return copyFunction(obj)
       }
       if (obj === null || typeof obj !== 'object') {
           return obj
       }
   
       if (Object.prototype.toString.call(obj) === '[object Date]') return new Date(obj)
       if (Object.prototype.toString.call(obj) === '[object RegExp]') return new RegExp(obj)
       if (Object.prototype.toString.call(obj) === '[object Error]') return new Error(obj)
      
       const item = cache.filter(item => item.original === obj)[0]
       if (item) return item.copy
       
       let copy = Array.isArray(obj) ? [] : {}
       cache.push({
           original: obj,
           copy
       })
   
       Object.keys(obj).forEach(key => {
           copy[key] = deepCopy(obj[key], cache)
       })
   
       return copy
   }
   deepCopy($obj).func === $obj.func // false
   
   ```

   
