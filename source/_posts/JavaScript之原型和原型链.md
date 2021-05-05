## JavaScript之原型和原型链

在JavaScript的学习过程中，原型链肯定是一个很重要的知识点，下面我们就来深入的去看一下原型链

### 原型

首先看一个很简单的例子

```javascript
function Person(name, age) {
            this.name = name;
            this.age = age;
            this.sayName = function () {
                console.log("i am "+this.name);
            }
        }
        Person.prototype.sayAge = function () {
            console.log("i am "+this.age);
        }
        var person1 = new Person("ljm",18)
        person1.sayAge();
        person1.sayName();
        var person2 = new Person("lll",20);
        person2.sayAge();
        person2.sayName();
```

控制台输出的是，和大多数人预想的一样，输出了两个person的年龄和姓名

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200712234246940.png)

在这个过程中我们创建了两个实例对象（person1，person2），有一个构造函数（Person）和原型（Person.prototype），那么他们之间的关系应该是这样的

在这里插入图片描述
在这个关系上我们可以看出

1. 每个函数上面都有一个属性(prototype)指向了函数的原型对象(Person.prototype)。
2. 每个实例上面都有一个隐式原型(proto)指向了函数的原型对象，如本利的p1对象有一个隐式原型也指向了Person.prototype对象。
3. 每个函数的原型对象上面都有一个constructor属性，指向了构造函数本身。

实例的（person1，person2）的`__proto__`也是Person.prototype

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200712234355210.png)

那么在实例访问属性或者方法的时候应该遵循什么样的原则？

1. 如果实例上面存在，就用实例本身的属性和方法。
2. 如果实例上面不存在，就会顺着`__proto__`的指向一直往上查找，找到就停止，找不到就报错。

```javascript
function Person(name, age) {
            this.name = name;
            this.age = age;
            this.sayName = function () {
                console.log("i am "+this.name);
            }
        }
        Person.prototype.sayAge = function () {
            console.log("我是原型上的sayAge");
        }
        var person1 = new Person("ljm",18)
        person1.sayAge = function () {
            console.log("我是person1上的sayAge")
        }
        person1.sayAge();
        person1.sayName();
        var person2 = new Person("lll",20);
        person2.sayAge();
        person2.sayName();
		person2.sayHello();
```

那么输出的结果就是

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200712234520190.png)

如果找不到这个方法，就报错

在这里插入图片描述
虽然person1和person2都输出了sayAge方法，可是这两个方法是不一样的

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200712234553921.png)

### 原型链

那么在刚刚这个输出sayAge过程大概可以这样描述：

在person1上有sayAge，所以输出的就是person1上的sayAge

在person2上没有sayAge，但是在person2的原型上有sayAge，所以输出的就是person2上的sayAge

简单点说就是，如果我自己有，我就用我自己的；如果我没有，则去我的上一级找；这样就形成了一条原型链。上一节中Person的原型其实还有一属性`__proto__`，他指向了上一级Object的原型对象。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200712234610508.png)
Object.prototype可以说是原型中的最高层，那么最终原型链应该是这样的

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200712234624390.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70)

### 原型和实例之间的判断

1. 使用instanceof

   ```javascript
           console.log(person1 instanceof Person)//true
           console.log(person1 instanceof Object)//true
   ```

2. 使用isPrototypeOf()方法

   ```javascript
           console.log(Object.prototype.isPrototypeOf(person1))//true
           console.log(Person.prototype.isPrototypeOf(person1))//true
           console.log(Function.prototype.isPrototypeOf(person1))//false
   ```

### 在ES6中的新运用

在ES6中，新运用了class来对JavaScript的语法更加加以完善。

在其他语言中，例如java，就有类（class），里面有构造函数（constructor），属性，方法等

所以在ES6中，为了弥补之前的不足，新推出了class

在以前的js中，生成一个对象实例，需要先定义构造函数，然后通过prototype 的方式来添加方法，再生成实例，但是现在ES6可以这样写

```javascript
        class Person{
            constructor(age,name){
                this.age = age;
                this.name = name;
            }
            sayName(){
                console.log("我是"+this.name)
            }
        }
        var person1 = new Person(18,"ljm")
        console.log(person1.name)//ljm
        console.log(person1.age)//18
        person1.sayName()//我是ljm
```

在ES5中原本的构造函数被constructor 替代，本来需要定义在prototype上面的，方法直接定义在class里面即可。

### 继承

在ES6中可以使用extends来继承类（和java差不多）

```javascript
class Student extends Person{
            constructor(age,name,className){
                super(age,name);
                this.className = className;
            }
            sayClass(){
                console.log("我是"+this.className)
            }
        }
        var student1 = new Student(19,"ljm","四班");
        console.log(student1.age)//19
        console.log(student1.name)//ljm
        console.log(student1.className)//四班
        student1.sayClass()//我是四班
        student1.sayName()//我是ljm
```

在ES5中prototype的继承

```javascript
        function Person(age,name) {
            this.age = age;
            this.name = name;
        }
        Person.prototype.sayName = function () {
            console.log("我是"+this.name)
        }
        function Student(age,name,className) {
            Person.call(this,age,name);
            this.className = className;
        }
        Person.prototype.constructor = Student;
        Student.prototype = new Person;
        Student.prototype.sayClass = function () {
            console.log("我是"+this.className)
        }
        var student1 = new Student(19,"ljm","四班")
        student1.sayClass();
        student1.sayName();
```

在ES6语法上更加便捷

