## js和jq动态添加和删除元素
js部分:

 1. 添加元素
```javascript
 var div = document.createElement('div');
 var test = document.getElementById('test');
 test.appendChild(div)
```
 2. 删除元素
```javascript
 var div = document.getElementsByClassName('divList')[0];
 var test = document.getElementById('test');
 test.removeChild(div)
```
jq部分

 1. 结尾添加元素`append()`
 2. 开头添加元素`prepend()`
 3. 在某个元素前添加元素`before()`
 4. 在某个元素之后添加元素`after()`
 5. 删除元素`remove()`
 6. 清空元素`empty()`

