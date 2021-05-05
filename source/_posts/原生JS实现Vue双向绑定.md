---
title: 原生JS实现Vue双向绑定
date: 2020-08-01 21:35:43
tags:
	- 前端
	- JavaScript
	- Vue
categories: 前端
---

## 原生Js实现Vue双向绑定

Vue数据双向数据绑定是通过发布者-订阅者模式来实现的，在正常使用Vue的时候，调用data里的属性是这样的

```js
var vm = new Vue({
    data: {
        obj: {
            a: 1
        }
    },
    created: function () {
        console.log(this.obj);
    }
});
```

![1596277495272](1596277495272.png)

里面有一个set和get，这是使用了`Object`里面的`defineProperty`，我们新建一个空对象，使用一下这个函数

```js
var Person = {};
var name = '';
Object.defineProperty(Person,'name',{
    set: function(value){
        console.log('我被设置了一个值叫:'+value);
        name = value;
    },
    get: function(){
        return '你的名字是'+name
    }
})
Person.name = 'LJM';
console.log(Person.name)
```

![1596277597282](1596277597282.png)

![1596277666179](1596277666179.png)

可以看出，我们对`defineProperty`函数的set和get方法进行了重写，而且结构也和Vue实现的结构差不多，所以Vue是通过`defineProperty`进行数据劫持的。

### 实现过程

1. 实现一个监听者observe，用来劫持并监听所有的属性，如果有数据变动，就通知所有的订阅者
2. 实现一个订阅者Watcher，收到属性变化通知后执行相应的函数，从而实现更新目的



#### 实现一个observe

```js
function defineReactive(data,key,value){
    observe(value);//遍历所有字属性
    var dep = new Dep(); //添加订阅者
    Object.defineProperty(data,  key, {
        enumerable: true,
        configurable: true,
        set(newVal){
            //新值旧值一样直接返回
            if (value === newVal) {
                return;
            }
            value = newVal;

            console.log('属性' + key + '已经被监听了，现在值为：“' + newVal.toString() + '”');  
            //所有订阅者更新
            dep.notify();
        },
        get(){

            if (Dep.target) {
                dep.addSub(Dep.target); // 在这里添加一个订阅者
            }
            return value;
        }
    })

}
Dep.target = null;
function observe(data) {
    if(!data || typeof data !== 'object') {
        return;
    }
    Object.keys(data).forEach(key => {
        defineReactive(data, key, data[key]);
    })
}
```

#### 实现订阅者

```js
function Dep () {
    this.subs = [];
}
Dep.prototype = {
    addSub: function(sub) {
        this.subs.push(sub);
    },
    notify: function() {
        this.subs.forEach(function(sub) {
            sub.update();
        });
    }
};
//实现Watcher
function Watcher(vm, exp, cb) {
    this.vm = vm;
    //需要监听的字段名字
    this.exp = exp;
    this.cb = cb;
    this.value = this.get() // 将自己添加到订阅器的操作
}
Watcher.prototype = {
    update(){
        this.run();
    },
    run(){
        let value = this.vm.data[this.exp]
        let oldValue = this.value;
        if(value !== oldValue){
            this.value = value;
            this.cb.call(this.vm, value, oldValue);
        }
    },
    get(){
        Dep.target = this;//缓存
        let value = this.vm.data[this.exp];
        Dep.target = null;//释放
        return value;
    }
}
```

#### 把监听者和订阅者相关联

```js
function SelfVue (data, el, exp) {
    var self = this;
    this.data = data;

    Object.keys(data).forEach(function(key) {
        self.proxyKeys(key);  // 绑定代理属性
    });

    observe(data);
    el.innerHTML = this.data[exp];  // 初始化模板数据的值
    new Watcher(this, exp, function (value) {
        el.innerHTML = value;
    });
    return this;
}

SelfVue.prototype = {
    proxyKeys: function (key) {
        var self = this;
        Object.defineProperty(this, key, {
            enumerable: false,
            configurable: true,
            get: function proxyGetter() {
                return self.data[key];
            },
            set: function proxySetter(newVal) {
                self.data[key] = newVal;
            }
        });
    }
}
```

#### 实现变换

```js
var ele = document.querySelector('#name');
var selfVue = new SelfVue({
    name: 'hello world'
}, ele, 'name');

window.setTimeout(function () {
    console.log('name值改变了');
    selfVue.name = 'canfoo';
}, 2000);
```

整个的代码

```js
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>双向绑定</title>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
</head>
<body>
   
    <h1 id="name">{ { name } }</h1>

    <script>

        var vm = new Vue({
            data: {
                obj: {
                    a: 1
                }
            },
            created: function () {
                console.log(this.obj);
            }
        });
        var person = {
            name: 'LJM'
        }
        console.log(person.name)
        var Person = {};
        var name = '';
        Object.defineProperty(Person,'name',{
            set: function(value){
                console.log('我被设置了一个值叫:'+value);
                name = value;
            },
            get: function(){
                return '你的名字是'+name
            }
        })
        Person.name = 'LJM';
        console.log(Person.name)
        //JS实现Vue
        function defineReactive(data,key,value){
            observe(value);//遍历所有字属性
            var dep = new Dep(); //添加订阅者
            Object.defineProperty(data,  key, {
                enumerable: true,
                configurable: true,
                set(newVal){
                    //新值旧值一样直接返回
                    if (value === newVal) {
                        return;
                    }
                    value = newVal;
                    
                    console.log('属性' + key + '已经被监听了，现在值为：“' + newVal.toString() + '”');  
                    //所有订阅者更新
                    dep.notify();
                },
                get(){
                    
                    if (Dep.target) {
                        dep.addSub(Dep.target); // 在这里添加一个订阅者
                    }
                    return value;
                }
            })

        }
        Dep.target = null;
        function observe(data) {
            if(!data || typeof data !== 'object') {
                return;
            }
            Object.keys(data).forEach(key => {
                defineReactive(data, key, data[key]);
            })
        }
        var library = {
            book1: {
                name: ''
            },
            book2: ''
        };
        // observe(library);
        // library.book1.name = 'vue权威指南'; // 属性name已经被监听了，现在值为：“vue权威指南”
        // library.book2 = '没有此书籍';  // 属性book2已经被监听了，现在值为：“没有此书籍”
        //植入订阅器
        function Dep () {
            this.subs = [];
        }
        Dep.prototype = {
            addSub: function(sub) {
                this.subs.push(sub);
            },
            notify: function() {
                this.subs.forEach(function(sub) {
                    sub.update();
                });
            }
        };
        //实现Watcher
        function Watcher(vm, exp, cb) {
            this.vm = vm;
            //需要监听的字段名字
            this.exp = exp;
            this.cb = cb;
            this.value = this.get() // 将自己添加到订阅器的操作
        }
        Watcher.prototype = {
            update(){
                this.run();
            },
            run(){
                let value = this.vm.data[this.exp]
                let oldValue = this.value;
                if(value !== oldValue){
                    this.value = value;
                    this.cb.call(this.vm, value, oldValue);
                }
            },
            get(){
                Dep.target = this;//缓存
                let value = this.vm.data[this.exp];
                Dep.target = null;//释放
                return value;
            }
        }
        //把observe和watcher关联
        function SelfVue (data, el, exp) {
            var self = this;
            this.data = data;
        
            Object.keys(data).forEach(function(key) {
                self.proxyKeys(key);  // 绑定代理属性
            });
        
            observe(data);
            el.innerHTML = this.data[exp];  // 初始化模板数据的值
            new Watcher(this, exp, function (value) {
                el.innerHTML = value;
            });
            return this;
        }
        
        SelfVue.prototype = {
            proxyKeys: function (key) {
                var self = this;
                Object.defineProperty(this, key, {
                    enumerable: false,
                    configurable: true,
                    get: function proxyGetter() {
                        return self.data[key];
                    },
                    set: function proxySetter(newVal) {
                        self.data[key] = newVal;
                    }
                });
            }
        }
        var ele = document.querySelector('#name');
        var selfVue = new SelfVue({
            name: 'hello world'
        }, ele, 'name');
    
        window.setTimeout(function () {
            console.log('name值改变了');
            selfVue.name = 'canfoo';
        }, 2000);
    </script>
</body>
</html>
```

![1596278186597](1596278186597.png)

![1596278197253](1596278197253.png)

确实在两秒后变换了数据，但是现在还没有解析器去解析`{ { } }`是直接利用`innerHtml`来替换里面的所有数据

所以，还要加一个Compile模板解析器

##### 实现Compile

1. 解析模板指令，替换数据，初始化视图

2. 对应的节点绑定对应的更新函数，初始化相应的订阅器

   ```js
   class Compile  {
       constructor(el, vm){
           this.vm = vm;
           this.el = document.querySelector(el.el);
           this.fragment = null;
           this.init();
       }
       init() {
           if (this.el) {
               this.fragment = this.nodeToFragment(this.el);
               this.compileElement(this.fragment);
               this.el.appendChild(this.fragment);
           } else {
               console.log('Dom元素不存在');
           }
       }
       nodeToFragment (el) {
           var fragment = document.createDocumentFragment();
           var child = el.firstChild;
           while (child) {
               // 将Dom元素移入fragment中
               fragment.appendChild(child);
               child = el.firstChild
           }
           return fragment;
       }
       compileElement(el) {
           var childNodes = el.childNodes;
           var self = this;
           [].slice.call(childNodes).forEach(function(node) {
               var reg = /\{\{(.*)\}\}/;
               var text = node.textContent;
   
               if (self.isElementNode(node)) {  
                   self.compile(node);
               } else if (self.isTextNode(node) && reg.test(text)) {
                   self.compileText(node, reg.exec(text)[1]);
               }
   
               if (node.childNodes && node.childNodes.length) {
                   self.compileElement(node);
               }
           });
       }
       compile(node) {
           var nodeAttrs = node.attributes;
           var self = this;
           Array.prototype.forEach.call(nodeAttrs, function(attr) {
               var attrName = attr.name;
               if (self.isDirective(attrName)) {
                   var exp = attr.value;
                   var dir = attrName.substring(2);
                   if (self.isEventDirective(dir)) {  // 事件指令
                       self.compileEvent(node, self.vm, exp, dir);
                   } else {  // v-model 指令
                       self.compileModel(node, self.vm, exp, dir);
                   }
                   node.removeAttribute(attrName);
               }
           });
       }
       compileText(node, exp) {
           var self = this;
           var initText = this.vm[exp];
           this.updateText(node, initText);
           new Watcher(this.vm, exp, function (value) {
               self.updateText(node, value);
           });
       }
       compileEvent(node, vm, exp, dir) {
           var eventType = dir.split(':')[1];
           var cb = vm.methods && vm.methods[exp];
   
           if (eventType && cb) {
               node.addEventListener(eventType, cb.bind(vm), false);
           }
       }
       compileModel(node, vm, exp, dir) {
           var self = this;
           var val = this.vm[exp];
           this.modelUpdater(node, val);
           new Watcher(this.vm, exp, function (value) {
               self.modelUpdater(node, value);
           });
   
           node.addEventListener('input', function(e) {
               var newValue = e.target.value;
               if (val === newValue) {
                   return;
               }
               self.vm[exp] = newValue;
               val = newValue;
           });
       }
       updateText (node, value) {
           node.textContent = typeof value == 'undefined' ? '' : value;
       }
       modelUpdater(node, value, oldValue) {
           node.value = typeof value == 'undefined' ? '' : value;
       }
       isDirective(attr) {
           return attr.indexOf('v-') == 0;
       }
       isEventDirective(dir) {
           return dir.indexOf('on:') === 0;
       }
       isElementNode (node) {
           return node.nodeType == 1;
       }
       isTextNode(node) {
           return node.nodeType == 3;
       }
   }
   ```

##### 整合

```js
class SelfVue{
    constructor(options){
        var self = this;
        this.data = options.data;
        this.methods = options.methods;
        this.vm = this;
        Object.keys(this.data).forEach(function(key) {
            self.proxyKeys(key);  // 绑定代理属性
        });

        new observe(this.data);
        new Compile(options, this.vm);
        options.mounted.call(this); // 所有事情处理好后执行mounted函数
    }
    proxyKeys(key) {
        var self = this;
        Object.defineProperty(this, key, {
            enumerable: false,
            configurable: true,
            get: function getter () {
                return self.data[key];
            },
            set: function setter (newVal) {
                self.data[key] = newVal;
            }
        });
    }
}


class observe{
    constructor(data){
        if(!data || typeof data !== 'object') {
            return;
        }
        Object.keys(data).forEach(key => {
            this.defineReactive(data, key, data[key]);
        })
    }
    defineReactive(data,key,value){
        new observe(value);//遍历所有字属性
        var dep = new Dep(); //添加订阅者
        Object.defineProperty(data,  key, {
            enumerable: true,
            configurable: true,
            set(newVal){
                //新值旧值一样直接返回
                if (value === newVal) {
                    return;
                }
                value = newVal;
                
                console.log('属性' + key + '已经被监听了，现在值为：“' + newVal.toString() + '”');  
                //所有订阅者更新
                dep.notify();
            },
            get(){
                
                if (Dep.target) {
                    dep.addSub(Dep.target); // 在这里添加一个订阅者
                }
                return value;
            }
        })
    }
}
class Dep{
    constructor(){
        this.subs = [];
        this.target = null;
    }
    addSub(sub) {
        this.subs.push(sub);
    }
    notify() {
        this.subs.forEach(function(sub) {
            sub.update();
        });
    }
}
class Watcher{
    constructor(vm, exp, cb){
        this.vm = vm;
        //需要监听的字段名字
        this.exp = exp;
        this.cb = cb;
        this.value = this.get() // 将自己添加到订阅器的操作
    }
    update(){
        this.run();
    }
    run(){
        let value = this.vm.data[this.exp]
        let oldValue = this.value;
        if(value !== oldValue){
            this.value = value;
            this.cb.call(this.vm, value, oldValue);
        }
    }
    get(){
        Dep.target = this;//缓存
        let value = this.vm.data[this.exp];
        Dep.target = null;//释放
        return value;
    }
}
class Compile  {
    constructor(el, vm){
        this.vm = vm;
        this.el = document.querySelector(el.el);
        this.fragment = null;
        this.init();
    }
    init() {
        if (this.el) {
            this.fragment = this.nodeToFragment(this.el);
            this.compileElement(this.fragment);
            this.el.appendChild(this.fragment);
        } else {
            console.log('Dom元素不存在');
        }
    }
    nodeToFragment (el) {
        var fragment = document.createDocumentFragment();
        var child = el.firstChild;
        while (child) {
            // 将Dom元素移入fragment中
            fragment.appendChild(child);
            child = el.firstChild
        }
        return fragment;
    }
    compileElement(el) {
        var childNodes = el.childNodes;
        var self = this;
        [].slice.call(childNodes).forEach(function(node) {
            var reg = /\{\{(.*)\}\}/;
            var text = node.textContent;

            if (self.isElementNode(node)) {  
                self.compile(node);
            } else if (self.isTextNode(node) && reg.test(text)) {
                self.compileText(node, reg.exec(text)[1]);
            }

            if (node.childNodes && node.childNodes.length) {
                self.compileElement(node);
            }
        });
    }
    compile(node) {
        var nodeAttrs = node.attributes;
        var self = this;
        Array.prototype.forEach.call(nodeAttrs, function(attr) {
            var attrName = attr.name;
            if (self.isDirective(attrName)) {
                var exp = attr.value;
                var dir = attrName.substring(2);
                if (self.isEventDirective(dir)) {  // 事件指令
                    self.compileEvent(node, self.vm, exp, dir);
                } else {  // v-model 指令
                    self.compileModel(node, self.vm, exp, dir);
                }
                node.removeAttribute(attrName);
            }
        });
    }
    compileText(node, exp) {
        var self = this;
        var initText = this.vm[exp];
        this.updateText(node, initText);
        new Watcher(this.vm, exp, function (value) {
            self.updateText(node, value);
        });
    }
    compileEvent(node, vm, exp, dir) {
        var eventType = dir.split(':')[1];
        var cb = vm.methods && vm.methods[exp];

        if (eventType && cb) {
            node.addEventListener(eventType, cb.bind(vm), false);
        }
    }
    compileModel(node, vm, exp, dir) {
        var self = this;
        var val = this.vm[exp];
        this.modelUpdater(node, val);
        new Watcher(this.vm, exp, function (value) {
            self.modelUpdater(node, value);
        });

        node.addEventListener('input', function(e) {
            var newValue = e.target.value;
            if (val === newValue) {
                return;
            }
            self.vm[exp] = newValue;
            val = newValue;
        });
    }
    updateText (node, value) {
        node.textContent = typeof value == 'undefined' ? '' : value;
    }
    modelUpdater(node, value, oldValue) {
        node.value = typeof value == 'undefined' ? '' : value;
    }
    isDirective(attr) {
        return attr.indexOf('v-') == 0;
    }
    isEventDirective(dir) {
        return dir.indexOf('on:') === 0;
    }
    isElementNode (node) {
        return node.nodeType == 1;
    }
    isTextNode(node) {
        return node.nodeType == 3;
    }
}
```

```js
<div id="app">
    <h2>{ { title } }</h2>
    <input v-model="name">
    <h1>{ { name } }</h1>
    <button v-on:click="clickMe">click me!</button>
</div>
<script src="js/vueBind.js"></script>
<script>
    var selfVue = new SelfVue({
        el: '#app',
        data: {
            title: '您好',
            name: 'canfoo'
        },
        methods: {
            clickMe: function () {
                this.title = 'hello world';
            }
        },
        mounted: function () {
            window.setTimeout(() => {
                this.title = '你好';
            }, 1000);
        }
    });
</script>
```

![1596288579053](1596288579053.png)