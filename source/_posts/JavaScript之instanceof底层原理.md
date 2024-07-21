---
title: JavaScript之instanceof底层原理
date: 2024-06-30 14:47:11
tags:
  - 前端
categories:
  - 前端
---

## JavaScript之instanceof底层原理

instanceof可以用来判断某个实例是否属于某个它的父类型，也可以判断某个引用是否属于某个构造函数

### instanceof底层是怎么工作的

```js
function _instanceof(L,R){
	let R_prototype = R.prototype;
    let L_proto = L.__proto__;
    while(true){
		if(L_proto === null){
			return false;
        }
        if(L_proto === R_prototype){
			return true;
        }
        L_proto = L_proto.__proto__;
    }
}
```

测试一下

```js

function Person(name,age,sex){
 
     this.name = name;
 
     this.age = age;
 
     this.sex = sex;
 
}
 
function Student(score){
 
     this.score = score;
 
}
 
var per = new Person("小明",20,"男");

var stu = new Student(98);

console.log(per instanceof Person);  // true

console.log(stu instanceof Student);  // true

console.log(per instanceof Object);  // true

console.log(stu instanceof Object);  // true

console.log(_instanceof(per, Person))// true
console.log(_instanceof(stu, Student))// true
console.log(_instanceof(per, Object))// true
console.log(_instanceof(stu, Object))// true
console.log(_instanceof(per, Student))// false
```

