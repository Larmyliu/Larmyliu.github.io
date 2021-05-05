## JavaScript之模拟实现new

`new`是JavaScript的一个关键字，可以实例化构造函数

```js
function Person(name,age) {
	this.name = name;
    this.age = age;
}
Person.prototype.sayHi = function() {
    console.log("HI! i am " + this.name);
}
var person1 = new Person("ljm", 18);
console.log(person1.age);//18
person1.sayHi();//HI! i am ljm
person1.__proto__ === Person.prototype;//true
```

从这个例子可以看出

1. `new`可以实例化一个对象
2. 这个对象可以访问构造函数的属性
3. 这个对象可以访问构造函数原型上的属性
4. 对象的`__proto__`指向构造函数的原型

我们来模拟一下new的方法

```js
function newFunction(){
	let res = {};
	//拿到构造函数，并利用call挂载参数
    let construct = Array.prototype.shift.call(arguments);
	res.__proto__ = construct.prototype;
    //第一个参数是所有者，第二个是参数
    construct.apply(res, arguments);
    return res;
}
function Person(name,age) {
	this.name = name;
    this.age = age;
}
Person.prototype.sayHi = function() {
    console.log("HI! i am " + this.name);
}
var person1 = newFunction(Person, "ljm", 18)
console.log(person1.age);//18
person1.sayHi();//HI! i am ljm
person1.__proto__ === Person.prototype;//true
```

输出已经和上面一样了，但是new还有一个特性，如果构造函数return回一个对象，那么new操作返回构造函数的return的对象

```js
function Person(name, age) {
	this.name = name;
    this.age = age;
    return {
        inside: "test"
    }
}
var person1 = new Person("ljm", 18);
console.log(person1.name)//undefined
console.log(person1.inside)//test
console.log(person1.__proto__ === Object.prototype);// true
console.log(person1.__proto__ === Person.prototype);// false
```

再完善一下上面的`newFunction`

```js
function newFunction(){
	let res = {};//也可以写Object.create(null);
	//拿到构造函数，并利用call挂载参数
    let construct = Array.prototype.shift.call(arguments);
	res.__proto__ = construct.prototype;
    //第一个参数是所有者，第二个是参数
    let turn = construct.apply(res, arguments);
    return turn instanceof Object ? turn : res;
}
```

#### js中new操作符具体做了什么

1. 创建一个空对象，并且把this变量引用该对象，同时继承了该对象的原型

2. 属性和方法都被加入到this这个引用对象中

3. 新创建的对象有this引用，并且最后隐式返回this

**一个简易版的new**

```js
function _new(fn, ...arg) {
    const obj = Object.create(fn.prototype);
    const ret = fn.apply(obj, arg);
    return ret instanceof Object ? ret : obj;
}
```

