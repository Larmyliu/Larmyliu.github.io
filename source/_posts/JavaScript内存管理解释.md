## JavaScript内存管理解释

很多时候，对于JavaScript开发者可能对内存管理毫无了解，毕竟JavaScript引擎帮你解决了这个问题

从一方面来说，你会遇到类似于内存泄露的问题，只有你知道内存是怎么样工作的，你才能解决这个问题

在这篇文章，我会向你介绍如何分配内存和垃圾处理，同时，如何去避免普通的内存泄露

### 内存生命周期

在JavaScript中，当我们创建一个变量，函数，或者任何一个你可以想到的数据类型，JS引擎会为这些分配内存，当你再也不使用这些数据类型时，JS引擎会释放掉这些内存

**内存分配**是在内存中保留空间的过程，在释放内存的同时释放空间，以便用于其他目的。

每次我们分配一个变量或者创建一个函数，内存总是经过以下几个相同的状态

![Memory life cycle overview](http://felixgerschau.com/static/87cb911a5bdda814cdc38a1679e327e5/c1b63/memory-life-cycle.png)

- **分配**内存

  JavaScript非常照顾我们：他为我们创建的对象进行内存分配

- **使用**内存

  使用内存是我们在代码中明确做的事情：**读**和**写**对于内存而已不过是对变量的读写

- **释放**内存

  这一步同样是JavaScript引擎帮我们处理好了的。一旦分配的内存被释放了，释放出来的内存可以被用作新的目的



> 在内存管理上下文中，对象不仅包括JS对象，还包括函数和函数作用域。



### 内存堆和堆栈

我们都知道在JavaScript中对于任何我们创建的数据类型，引擎都会为其分配内存，当不再使用时释放掉

我的脑海中下一个问题就是：这些东西被存储在什么地方

JavaScript引擎有两个地方可以存储数据：内存堆和堆栈

堆和栈是两种数据结构，被内存引擎用在不同的目的



### 栈：静态内存器分配

> 在本系列关于调用堆栈和事件循环的第一部分中，您可能知道堆栈，在第一部分中，我重点介绍了如何使用它来跟踪JS解释器需要调用的函数。

![Memory stack Example](https://felixgerschau.com/static/b94165593eb6e02d73039d8b2cfccfdd/c1b63/stack-memory-explained.png)

堆栈是JavaScript用来存储静态数据的数据结构。静态数据是引擎在编译时知道其大小的数据。在JavaScript中，这包括基本值(字符串、数字、布尔值、undefined和null)和指向对象和函数的引用。

因为引擎知道大小不会改变，所以它会为每个值分配固定数量的内存。

在执行之前分配内存的过程称为静态内存分配。

因为引擎为这些值分配了固定数量的内存，所以对原始值的大小有限制。

这些值和整个堆栈的限制因浏览器而异。

### 堆:动态内存分配

堆是另一个存储数据的空间，JavaScript在其中存储对象和函数。

与堆栈不同，引擎不会为这些对象分配固定数量的内存。相反，将根据需要分配更多的空间。

这种分配内存的方式也称为动态内存分配。

为了得到一个概述，下面是这两个存储的特性对比:

这些值和整个堆栈的限制因浏览器而异。

| Stack                                                        | Heap                                                 |
| ------------------------------------------------------------ | ---------------------------------------------------- |
| 原始值和引用对象和函数，在编译时知道大小在运行时知道大小，为每个对象分配固定数量的内存 | 在运行时知道对象和函数的大小，没有对每个对象进行限制 |

### 例子

让我们看一些代码的例子，在注释中我提到了分配的内容:

```js
const person = {
  name: 'John',
  age: 24,
};
```

JS为这个对象在堆中分配了内存，实际值仍然是原始值，这就是它们存储在堆栈中的原因。

### 垃圾回收

我们都知道JavaScript为所有对象分配了内存，如果我们还记得内存生命周期，最后一步就是释放内存

和内存分配相似，JS引擎也为我们处理好了这一步，更具体而言，垃圾收集器来处理这个问题

一旦js引擎认出，一个曾经出现过的变量或者函数不再被使用，会释放掉它的内存

这样做的主要问题是，是否还需要一些内存是一个无法决定的问题，这意味着不可能有一种算法能够在不再需要的时候收集所有的内存

一些算法提供了一个很好的逼近问题。我将讨论最常用的:引用计数垃圾收集和标记和清除算法。

### 引用计数器垃圾收集

这是最简单的近似。它收集没有引用指向它们的对象

让我们看一个例子，线条表示引用

[例子视频](https://felixgerschau.com/video/stack-heap-gc-animation.mp4)

注意，在最后一个帧中，只有兴趣爱好留在堆中，因为它是在最后有引用的对象

### 循环

这个算法存在一个问题是没有考虑到循环引用，这个出现在当一个或者多个对象互相引用，但是不能被代码访问到了

```js
let son = {
  name: 'John',
};

let dad = {
  name: 'Johnson',
}

son.dad = dad;
dad.son = son;

son = null;
dad = null;
```

![Reference cycle illustrated](https://felixgerschau.com/static/30c115f91d53d20972287fa05592520c/5a190/reference-cycle.png)

将它们设置为null不会使引用计数算法识别出它们不再被使用，因为它们都有传入的引用。

### 标记和清扫算法标记

扫描算法解决了循环依赖的问题。它不是简单地计数对给定对象的引用，而是检测它们是否可从根对象访问。

浏览器的根目录是`window`对象，而在NodeJS中这是`global`的

![Mark-and-sweep algorithm illustrated](https://felixgerschau.com/static/87b4e1eb66afc84d49da13af8e897367/5a190/garbage-collectoion-algorithm.png)

该算法将不能到达的对象标记为垃圾，然后扫描(收集)它们。根对象永远不会被收集。

这样，循环依赖关系就不再是问题了。在前面的示例中，父对象和子对象都不能从根访问。因此，它们都将被标记为垃圾并被收集。

自2012年以来，该算法在所有现代浏览器中都得到了实现。改进的只是性能和实现，而不是算法的核心思想本身

### 权衡

自动垃圾收集使我们能够专注于构建应用程序，而不是在内存管理上浪费时间。然而，我们需要注意一些权衡。

### 内存使用

由于算法无法确切知道什么时候不再需要内存，Javascript应用程序可能会使用比实际需要更多的内存。

即使对象被标记为垃圾，也要由垃圾收集器决定何时以及是否要收集分配的内存。

如果您需要您的应用程序尽可能地提高内存效率，那么最好使用较低级别的语言。但请记住，这有它自己的一套权衡。

### 表现

为我们收集垃圾的算法通常定期运行以清理未使用的对象。

问题是，我们作为开发人员，不知道这到底什么时候会发生，收集大量垃圾或频繁收集垃圾可能会影响性能，因为这样做需要一定的计算能力然而。

然而，用户或开发人员通常不会注意到这种影响。

### 内存泄露

有了前面关于内存管理的知识，让我们看看最常见的内存泄漏，如果理解背后发生的事情，这些是可以轻松避免的。

#### 全局变量

在全局变量中存储数据可能是最常见的内存泄漏类型在浏览器的`JavaScript`中，

如果不使用`var`、`const`或`let`，则该变量将附加到`window`对象。

```js
users = getUsers();
```

在严格模式下运行代码可以避免这种情况。

除了意外地将变量添加到根目录之外，在许多情况下，您可能有意这样做。

您当然可以使用全局变量，但要确保在不再需要数据时释放空间。

要释放内存，将全局变量赋值为null

```js
window.users = null;
```

#### 忘记定时器和回调函数

忘记计时器和回调会使应用程序的内存使用量上升。特别是在单页应用程序(spa)中，在动态添加事件监听器和回调时必须小心。

#### 忘记定时器

```js
const object = {};
const intervalId = setInterval(function() {
  // everything used in here can't be collected
  // until the interval is cleared
  doSomething(object);
}, 2000);
```

上面的代码每2秒运行一次函数。

如果您的项目中有这样的代码，那么您可能不需要一直运行它。

只要这个间隔没有被取消，在这个间隔中引用的对象就不会被垃圾回收确保在不再需要间隔时清除它。

```js
clearInterval(intervalId);
```

这在单页面应用中尤为重要。即使当导航离开需要此间隔的页面时，它仍将在后台运行。

#### 忘记回调

假设您向按钮添加了一个`onclick`监听器，该监听器稍后将被删除。旧的浏览器无法收集监听器，但现在，这已经不是问题了。不过，当你不再需要事件监听器时，删除它们仍然是一个好主意。

```js
const element = document.getElementById('button');
const onClick = () => alert('hi');

element.addEventListener('click', onClick);

element.removeEventListener('click', onClick);
element.parentNode.removeChild(element);
```

#### 脱离DOM引用

此内存泄漏与前面的内存泄漏类似:它发生在用JavaScript存储DOM元素时。

```js
const elements = [];
const element = document.getElementById('button');
elements.push(element);

function removeAllElements() {
  elements.forEach((item) => {
    document.body.removeChild(document.getElementById(item.id))
  });
}
```

当您删除这些元素时，可能需要确保也从数组中删除该元素。

否则，就无法收集这些DOM元素。

```js
const elements = [];
const element = document.getElementById('button');
elements.push(element);

function removeAllElements() {
  elements.forEach((item, index) => {
    document.body.removeChild(document.getElementById(item.id));
    elements.splice(index, 1);
  });
}
```

由于每个DOM元素也保留对其父节点的引用，因此可以防止垃圾收集器收集元素的父元素和子元素。

## 总结

在本文中，我总结了JavaScript中内存管理的核心概念。

写这篇文章帮助我澄清了一些我还没有完全理解的概念，我希望这篇文章能够很好地概述`Javascript`中的内存管理是如何工作的。

