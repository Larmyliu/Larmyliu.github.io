## JavaScript之执行上下文（EC）

在JavaScript中，执行上下文是一个基本概念，在执行上下文中，包括了变量对象（VO，Variable Object），作用域链（Scope Chain），this指向等，在闭包，函数/变量提升息息相关

### 执行上下文

执行上下文可以理解为执行环境，执行环境定义了变量或者函数有权访问其他数据，决定他们各自的行为，执行环境主要包括了**全局上下文**，**函数上下文**，**eval执行上下文**。主要理解的是全局上下文和函数上下文。

在JavaScript代码执行的过程中，首先进入的就是全局环境，每个函数都有自己的执行环境。所以我们分别叫他们全局上下文和函数上下文

### 如何执行

执行上下文是以栈的方式被存放起来的，称这个栈为执行上下文栈。

在JavaScript代码开始执行时，将全局上下文创建并入栈，当调用函数时，会进入对应函数的环境，创建函数上下文并入栈，当栈顶的执行上下文代码执行完毕后，则将其出栈。

一个简单的例子

```javascript
function fn2() {
  console.log('fn2')
}
function fn1() {
  console.log('fn1')
  fn2();
}
fn1();
```

执行过程中的执行上下文栈

```JavaScript
/* 伪代码 以数组来表示执行上下文栈 ECStack=[] */
// 代码执行时最先进入全局环境，全局上下文被创建并入栈
ECStack.push(global_EC);
// fn1 被调用，fn1 函数上下文被创建并入栈
ECStack.push(fn1_EC);
// fn1 中调用 fn2，fn2 函数上下文被创建并入栈
ECStack.push(fn2_EC);
// fn2 执行完毕，fn2 函数上下文出栈
ECStack.pop();
// fn1 执行完毕，fn1 函数上下文出栈
ECStack.pop();
// 代码执行完毕，全局上下文出栈
ECStack.pop();
```

在一个执行上下文中，最重要的三个属性分别是**变量对象（Variable Object）**、**作用域链（Scope Chain）**和 **this 指向**。

我们可以采用如下方式表示



```js
EC = {
  VO,
  SC,
  this
}
```

一个执行上下文的生命周期分为**创建**和**执行**阶段。创建阶段主要工作是**生成变量对象**、**建立作用域链**和**确定 this 指向**。而执行阶段主要工作是变量赋值以及执行其它代码等。

### 变量对象（VO）

在执行上下文的创建阶段会生成变量对象，主要是以下三个过程

1. **检索当前上下文中的参数**，该过程生成的Arguments对象，并建立形参
2. **检索当前上下文中的函数声明**，该过程建立以函数名为属性名，函数所在内存地址引用为属性值的属性；
3. **检索当前上下文中的变量声明**，该过程建立以变量名为属性名，undefined 为属性值的属性（如果变量名跟已声明的形参变量名或函数名相同，则**该变量声明**不会干扰已经存在的这类属性）。

```javascript
VO = {
  Arguments: {}, 
  ParamVariable: 具体值,  //函数传递的形参变量
  Function: <function reference>,
  Variable:undefined//内部声明的变量
}
```

当执行上下文进入执行状态后，变量对象会变为**活动对象**（Active Object，AO），此时声明的变量会进行赋值

```js
AO = {
  Arguments: {},
  ParamVariable: 具体值,  //函数传递的形参变量
  Function: <function reference>,
  Variable:具体值//内部声明的变量
}
```

同样举例来表明变化情况

```js
function fn1(a) {
  var b = 1;
  function fn2() {}
  var c = function () {};
}
fn1(0);
```

在创建阶段

```js
VO = {
	Arguments:{
		'0':0,
        length:1
    },
    a:0,
    b:undefined,
    fn2:<function fn2 reference>,
    c:undefined
}
```

在执行阶段

```js
AO = {
	Arguments:{
		'0':0,
        length:1
    },
    a:0,
    b:1,
    fn2:<function fn2 reference>,
    c:<function express c reference>
}
```

理解后就可以更好的指导**函数提升**和**变量提升**的内在机制

```js
console.log(a)//undefined
fn(0);//fn
var a = 0;
function fn() {
	console.log("fn")
}
```

在全局上下文的创建阶段，先会检索函数声明和变量声明，函数声明会被赋值具体引用地址，变量声明会赋值undefined

所以具体执行应该是这样的

```js
function fn() {
	console.log("fn");
}
var a = undefined;
console.log(a)//undefined
fn(0)//fn
a = 0;
```

### 作用域链（Scope Chain）

**作用域链是指由当前上下文和上下层上下文的一系列变量对象形成的层级链**。保证了当前执行环境对符合访问权限的变量和函的有序访问

在执行上下文分成两个阶段，一个是创建一个是执行。在执行上下文阶段，如果需要查找某个变量或者函数时，会从当前上下文中查找，如果没有找到，就会沿着上层上下文进行查找，直到找到全局上下文

来个简单的例子

```js
var a = 1;
function fn1(){
    function fn2(){
		function fn3(){
			console.log(a);//1
        }
        fn3();
    }
    fn2();
}
fn1()
```

作用域链是fn3→fn2→fn1→global，从fn3的作用域中开始查找a，找不到一直往上查找，最终在全局环境下找到了a

作用域访问就是里面可以访问外面的，外面的不能访问里面的。

执行上下文中的作用域链是如何建立的？

在JavaScript中主要包含了全局作用域和函数作用域，函数作用域是在函数被声明的时候确定的。

每一个函数都会包含一个[[scope]]内部属性，在函数被声明的时候，该函数的[[scope]]属性会保存其上下文的变量对象，形成层级链。**[[scope]]属性的值是在函数被声明的时候确定的**

在函数调用的时候，其执行上下文会被创建并且入栈。在创建阶段生成的变量对象，会将该变量对象添加到作用域的顶端并将[[scope]]添加进该作用域链中，在执行阶段，变量对象会变成活动对象，相应属性被赋值。

所以，作用域链是由当前上下文变量对象及上层上下文变量对象组成的

```js
SC = AO + [[scope]]
```

来一个例子

```js
var a = 1;
function fn1() {
  var b = 1;
  function fn2() {
    var c = 1;
  }
  fn2();
}
fn1();
```

在创建阶段，fn1中fn2被声明，所以

```js
fn2.[[scope]]=[fn1_EC.VO, globalObj]
```

当 fn2 被调用的时候，其执行上下文被创建并入栈，此时会将生成的变量对象添加进作用域链的顶端，并且将 [[scope]] 添加进作用域链

```js
fn2_EC.SC=[fn2_EC.VO].concat(fn2.[[scope]])
=>
fn2_EC.SC=[fn2_EC.VO, fn1_EC.VO, globalObj]
```

### this指向

**this的指向，是在函数被调用的时候确定的**。也就是在执行上下文被创建的时候确定的。

this 的指向，最主要的是三种场景，分别是**全局上下文中 this**、**函数中 this** 和**构造函数中 this**。

#### 全局上下文中 this

在全局上下文中，this 指代全局对象。

```js
//在浏览器环境中，全局对象是window对象
console.log(this === window)//true
var a = 1;
this.b = 2;
console.log(window.a)//1
console.log(window.b)//2
console.log(b)//2
```

#### 函数中的this

**如果被调用的函数，被某一个对象所拥有，那么其内部的 this 指向该对象；如果该函数被独立调用，那么其内部的 this 指向 undefined（非严格模式下指向 window）。**

```js
var a = 1;
function fn() {
  console.log(this.a)
}
var obj = {
  a: 2,
  fn: fn
}
obj.fn(); // 2
fn(); // 1
```

#### 构造函数中的this

**对于构造函数来说，其内部 this 指向新创建的对象实例**。

```js
function Person(name, age) {
  this.name = name;
  this.age = age;
}
var ttsy = new Person('ljm', 18);
console.log(ttsy.name);  // ljm
console.log(ttsy.age);  // 18
```

**在 ES6 中箭头函数中，this 是在函数声明的时候确定的，具体可看**

[https://es6.ruanyifeng.com/#docs/function]()
