## JavaScript之闭包理解

闭包（Closure）是JavaScript学习过程中一个非常重要的问题

闭包和执行上下文和作用域链有着千丝万缕的关系。闭包是指**有权访问另一个函数作用域的变量的函数**（JavaScript高级程序设计（第三版）P178）

**闭包是一个函数**，对闭包的一个理解可以是**所在的执行上下文已经出栈,但是仍然访问了其所在的执行上下文变量对象的函数**

这里所指的所在的执行上下文指的是闭包函数对应的执行上下文，而不是闭包本省所对应的执行上下文

来一个简单的例子

```js
function A(){
	var a = 2;
    function B(){
		console.log(a);
    }
    return B;
}
A()();
```

那么他的执行上下文栈行为是这样的

```js
/*伪代码*/
// 代码执行时最先进入全局环境，全局上下文被创建并入栈
ECStack.push(globalContext);
// A 被调用，A 函数上下文被创建并入栈
ECStack.push(<A> functionContext);
// A 执行完毕，A 函数上下文出栈
ECStack.pop();
// B 被调用，B 函数上下文被创建并入栈
ECStack.push(<B> functionContext);
// B 执行完毕，B 函数上下文出栈
ECStack.pop();
// 代码执行完毕，全局上下文出栈
ECStack.pop();
```

我们都知道，JavaScript 拥有自动的垃圾回收机制，当一个值失去引用的时候，垃圾回收机制会根据特殊的算法找到它并将其回收。

**函数的执行上下文在出栈后，其变量对象会失去引用等待被回收，而闭包的存在会阻止这一过程，因为闭包的作用域链包含了其所在执行上下文的变量对象。**

通过上面的代码可以看出，在B函数执行的时候，A的上下文已经出栈了，按照JavaScript的垃圾回收机制，A上下文的变量对象失去引用后会被垃圾回收机制回收，但是由于B上下文作用域链包含了A上下文的变量对象，所以A上下文的变量对象不会被垃圾回收机制回收。

我们知道**函数作用域是在函数被定义（声明）的时候确定的**。每一个函数都会包含一个[[scope]]内部属性，在函数被定义的时候，该函数的[[scope]]属性会保存其上层上下文的变量对象，形成包含上层上下文变量对象的层级链。

那么刚刚的代码上下文应该是这样的,在创建的时候

```js
B.[[scope]]=[AContext.VO,globalContext.VO]
```

在B被调用的时候，其执行上下文会被创建并入栈，此时会生成变量对象并将该变量对象添加进作用域链的顶端，并将[[scope]]添加进作用域链

```js
BContext.Scope = [BContext.VO].concat(B.[[scope]])
=>
BContext.Scope = [BContext.VO,AContext.VO,globalContext.VO]
```

可见，B上下文的作用域链包含了A上下文的变量对象，并且由于B访问A中的变量，阻止了A上下文的变量对象被垃圾回收机制回收。

看一个面试题经常会遇到的一个关于闭包的很经典的题目

```jsx
var arr = [];
for (var i = 0; i < 3; i++) {
    arr[i] = function () {
        console.log(i);
    };
}

arr[0]();//3
arr[1]();//3
arr[2]();//3
```

在 arr[0] 函数执行之前，我们可以知道，全局上下文的变量对象如下所示

```js
globalContext = {
	VO:{
		arr:[],
        i=3
    }
}
```

在 arr[0] 被调用执行时，其作用域链在函数上下文的创建阶段被创建，其作用域链如下

```undefined
arr[0]Context = {
    Scope: [arr[0]Context.VO, globalContext.VO]
}
```

arr[0]函数会在自身变量对象中寻找i（arr总没有i），所以会向上找，找到全局上下文变量对象中的i，所以输出3

那么如何解决闭包

### 立即执行函数

```js
var arr =  [];
for(var i = 0; i < 3; i++){
	arr[i] =  (function(j) {
        return function(){
			console.log(j);
        }
    })(i)
}
arr[0]();//0
arr[1]();//1
arr[2]();//2
```

### ES6中的let

```js
var arr = [];
for (let i = 0; i < 3; i++) {
    arr[i] = function () {
        console.log(i);
    };
}

arr[0]();//0
arr[1]();//1
arr[2]();//2
```

### setTimeout

也还可以利用setTimeout的第三个参数

```js
    var arr = [];
    for(var i = 0; i < 3; i++){
        arr[i] = setTimeout(function(i){
            console.log(i);
        },0,i)
    }
```


