---
title: XMLHttpRequest、JSONP的适用场景
date: 2020-11-06 13:16:38
tags:
	- 前端
	- 笔记
categories: 前端
---

## XMLHttpRequest、JSONP的适用场景

1. `XMLHttpRequest`（XHR）对象用于与服务器交互。通过 XMLHttpRequest 可以在不刷新页面的情况下请求特定 URL，获取数据。ajax就是基于`XMLHttpRequest`来发送数据的，支持get和post请求 ，一般用于非跨域场景，如果需要跨域，需要配置`cors`头。而JSONP常用于跨域场景，仅支持get请求，同时服务器也要支持jsonp请求
2. `XMLHttpRequest`异常判断通过http状态码和readyStatus来判断，JSONP的异常判断通过onerror事件和timer来判断