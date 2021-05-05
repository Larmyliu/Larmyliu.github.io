## vue报错之this.$router is undefine
最近写项目遇到这个报错，特此记录一下

```javascript
 axios.get('/person/ticket')
 .then(response => {
    
   	this.$router.push('/ground')
           
 })
```
原因很简单，因为我使用的是箭头函数，改变了this指向，所以vue在这里找不到$router，从而报错

解决很简单，保留this即可

```javascript
 const that = this;
 axios.get('/person/ticket')
 .then(response => {
    
   	that.$router.push('/ground')
           
 })
```

