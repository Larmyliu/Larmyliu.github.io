---
title: Cannot set headers after they are sent to the client
date: 2020-08-05 14:14:00
tags: 
	- 前端
	- JavaScript
	- nodeJS
categories: nodeJS
---

﻿在nodeJs连接mysql返回语句时老是报这个错误，原因返回了多次响应，所以在每一次返回语句中，要及时加上return


没修改前

```javascript
app.get('/addScore', function(req,res){
  let query = req.query;
  console.log(query)
  var sql = `update team set totalScore = totalScore+${query.score}, count = count+1 where id = ${query.id};`
  connection.getConnection(function(err, conn){
    if (err) {
      res.send(JSON.stringify({
        message:'服务器有问题',
        status:500
      }))
    }
    else{
      conn.query(sql,function(error, result){
        if(error){
          res.send(JSON.stringify({
            message:"找不到资源",
            status:404
          }))
        }
        res.send(JSON.stringify({
          message:"成功修改",
          status:200
          
        }))
      })
    }
  })
})
```
修改之后

```javascript
app.get('/addScore', function(req,res){
  let query = req.query;
  console.log(query)
  var sql = `update team set totalScore = totalScore+${query.score}, count = count+1 where id = ${query.id};`
  connection.getConnection(function(err, conn){
    if (err) {
      return res.send(JSON.stringify({
        message:'服务器有问题',
        status:500
      }))
    }
    else{
      conn.query(sql,function(error, result){
        if(error){
          return res.send(JSON.stringify({
            message:"找不到资源",
            status:404
          }))
        }
        return res.send(JSON.stringify({
          message:"成功修改",
          status:200
          
        }))
      })
    }
  })
})
```

