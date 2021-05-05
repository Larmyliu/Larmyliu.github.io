---
title: node连接mysql数据库，封装增删改查
date: 2020-08-05 14:00:00
tags: 
	- 前端
	- JavaScript
	- nodeJS
categories: nodeJS
---

﻿最近写了一下node，用nodejs和vue写了一个很简易的评分管理系统，总计一下在node连接mysql

首先，我们需要安装一下`mysql`的模块,在你服务器工作目录下执行命令

```javascript
npm install mysql --save
```
在js中引入该模块，配置一下端口号，密码什么的

```javascript
const mysql = require("mysql");
var connection = mysql.createPool({
   host: "localhost",
   user: "root",
   password: "123456",
   database: "nodetest"
}) //数据库连接配置
```
接下来来一个简单的测试，我用的是express框架

```javascript
app.get('/', function (req, res, next) {
  connection.getConnection(function(err,conn){
    if(err){
      return;
    }
    else{
      conn.query("select * from team",function(err,result){
        return res.send({
          message:"成功",
          data:result
        })
      })
    }
  })
});
```
成功拿到数据
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020080511262243.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70)
在写的过程中，发现有很多地方需要写重复的语句，所以把增删改查封装一下

```javascript
const mysql = require("mysql");
class db  {
  constructor(databaseName){
    this.connection = mysql.createPool({
        host:"localhost",
        user:"root",
        password:"123456",
        database:databaseName
    })
    this.common = [
      {
        message: "服务器出现问题",
        status: 500
      },
      {
        message: "无法访问资源",
        status: 404
      },
    ]
  }
  /**
   * 
   * @param {*} name 要查询的字段名称,用(,)隔开
   * 
   * @param {*} tableName 要查询的表名
   * 
   * @param {*} options 选择条件,字符串形式,要写where，如where id = 1
   */
  select(name,tableName,options) {
    let self = this
    return new Promise((resolve, reject) =>{

      self.connection.getConnection(function (err, conn) {  
        if(err){
          console.log(err)
          reject (self.common[0])
        }else{
          let sql = `select ${name.toString()} from ${tableName} ${options}`
          console.log(sql)
          conn.query(sql,function (err, result) {  
            if(err){
              reject (self.common[1])
            }
            conn.release()
            console.log({
              status: 200,
              message:'成功',
              data:result
            })
            resolve({
              status: 200,
              message:'成功',
              data:result
            })
          })
        }
      })
    }).catch(err => err)
  }
  /**
   * 
   * @param {*} tableName 插入的表名
   * 
   * @param {*} name 插入的字段名,多个字段用(,)隔开 
   * 
   * @param {*} value 插入的字段值,多个字段用(,)隔开,字符串类型要加上引号
   */
  insert(tableName,name, value){
    let self = this;
    return new Promise((resolve,reject) => {
      self.connection.getConnection(function(err, conn){
        if(err){
          console.log(err)
          reject(self.common[0])
        }else{
          let sql = `insert into ${tableName} (${name}) values (${value})`
          conn.query(sql,function(err, result){
            if(err){
              console.log(err)
              reject(self.common[1])
            }
            conn.release();
            resolve({
              message:'成功',
              status:200,
              data: result
            })
          })
        }
      })
    }).catch(err =>{
      return err
    })
  }
  /**
   * 
  * @param {*} tableName 修改字段所在的表名
  * 
   * @param {*} name 需要修改的字段名称
   * 
   * @param {*} value 修改的值 
   * 
   * @param {*} option 特殊条件,要带有where
   */
  update(tableName,name,value, option){
    let self = this;
    return new Promise((resolve, reject) => {
      self.connection.getConnection(function(err, conn){
        if(err){
          console.log(err);
          reject (self.common[0])
        }else{
          let sql = `update ${tableName} set ${name} = ${value} ${option}`
          conn.query(sql, function(err, result) {
            if(err){
              console.log(err);
              reject(self.common[1])
            }
            conn.release();
            resolve({
              message:'成功',
              status:'200',
              data:result
            })
          })
        }
      })
    }).catch(err => err)
  }
  /**
   * 
   * @param {*} tableName 删除字段所在的表名
   * 
   * @param {*} option 特殊条件,要带有where
   */
  delete(tableName, option){
    let self  = this
    new Promise((resolve, reject) => {
      self.connection.getConnection(function(err, conn) {
        if(err){
          console.log(err);
          reject(self.common[0])
        }else{
          let sql = `DELETE FROM ${tableName} ${option}`
          conn.query(sql, function(err, result) {
            if(err){
              reject(self.common[1])
            }
            conn.release();
            console.log({
              data:result,
              message:"成功",
              status:200
            })
            resolve({
              data:result,
              message:"成功",
              status:200
            })
          })
        }
      })
    }).catch(err => err)
  }
}
module.exports = db
```
测试一下能不能正确返回

```javascript
var express = require('express');
var cors = require("cors");
var app = express();
app.use(cors())
var db =  require("../config/db.js");
const { json } = require('express');
var test = new db("nodetest");
app.get('/', async function (req, res, next) {  
  // let result = await test.select("id,totalScore","team","where id = 1")
  // let result = await test.insert("m_login","name,id","'LLLL',100")
  let result = await test.insert("m_losdsgin","name,id","'LLLL',100")
  // let result = await test.update("m_login","name","'xxx'","where id = 2")
  // let result = await test.delete("m_lowgin", "where id = 100")
  console.log(result)
  res.send(result)
});
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200805140522386.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200805140534276.png)
成功拿到数据
