---
title: ajax 的 readyState 有哪几个状态，含义分别是什么？
date: 2020-12-04 10:59:35
tags: 
	- 前端
	- 笔记
	- http
categories: 前端
---

## ajax 的 readyState 有哪几个状态，含义分别是什么？

1. 0未初始化状态

   创建了一个XMLHttpRequest对象

2. 1准备发送状态

   已经使用了XMLHttpRequest.open()方法，已经准备好一个请求发送到服务器端

3. 2已经发送状态

   已经通过send()方法发送请求，但是还没有得到响应

4. 3正在接受状态

   已经接受到服务器端返回的http响应，但还有部分消息体没有接收到

5. 4完成响应状态

   已经完成了HTTP响应的接收