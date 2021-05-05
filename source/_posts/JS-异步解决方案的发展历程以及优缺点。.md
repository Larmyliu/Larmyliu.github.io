---
title: JS 异步解决方案的发展历程以及优缺点
date: 2020-08-13 14:25:33
tags:
	- 前端
	- JavaScript
categories: 前端
---

### 1.回调函数（callback）

```js
setTimeout(() => {
	//回调函数内部
}, 1000)
```

缺点

1. **回调地狱**

   ![是](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1597310194315&di=d4de52d570d6086f9c7ed93b95afa753&imgtype=0&src=http%3A%2F%2Faliyunzixunbucket.oss-cn-beijing.aliyuncs.com%2Fjpg%2F2451f1b01d709d237735a2b453c7b9aa.jpg%3Fx-oss-process%3Dimage%2Fresize%2Cp_100%2Fauto-orient%2C1%2Fquality%2Cq_90%2Fformat%2Cjpg%2Fwatermark%2Cimage_eXVuY2VzaGk%3D%2Ct_100)

2. 不能用try catch捕获错误

3. 不能return

回调地狱的根本问题在于：

- 缺乏顺序性：回调地狱导致调试困难，和大脑的思维方式不符
- 嵌套函数存在耦合，一旦有所改动，牵一发动全身（**控制反转**）
- 嵌套函数过多的话，很难处理错误

优点

- 解决了同步的问题 

### 2.Promise

Promise就是为了解决callback所产生的问题

Promise实现了链式调用，也就是说每次then后返回的都是一个全新的Promise，如果我们在then中return，return 的结果会被 Promise.resolve() 包装

优点：**解决了回调地狱**

```js
axios.get(url1)
.then(res => {
    //操作
	return axios.get(url2)
}).then(res => {
    //操作
	return axios.get(url3)
}).then(res => {
	//操作
})
```

缺点：**无法取消Promise，错误需要通过catch来捕获**

### 3.Generator

**可控制函数的执行**

```js
function *fetch() {
    yield ajax('XXX1', () => {})
    yield ajax('XXX2', () => {})
    yield ajax('XXX3', () => {})
}
let it = fetch()
let result1 = it.next()
let result2 = it.next()
let result3 = it.next()
```

### 4.async/await

异步终极解决方案

优点：代码清晰，处理了回调地狱的问题

缺点：**await将异步改成同步代码，如果多个异步操作没有依赖性而使用 await 会导致性能上的降低。**

```js
async function test() {
  // 以下代码没有依赖性的话，完全可以使用 Promise.all 的方式
  // 如果有依赖性的话，其实就是解决回调地狱的例子了
  await fetch('XXX1')
  await fetch('XXX2')
  await fetch('XXX3')
}
```