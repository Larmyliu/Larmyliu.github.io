今天遇到了一个问题

```
<div class="main">
        <div class="xxx"></div>
        <div class="box">
            aaa
            <div class="box1">
                ddd
            </div>
        </div>
        <div class="box">
            bbb
        </div>
        <div class="box">
            ccc
        </div>
    </div>
```
如何选中.main中的第二个box
首先不能使用:first-child,因为在.box前面有一个兄弟节点.xxx，会导致:first-child失效

紧接着想到用:first-of-type，可是写成`.box:first-of-type`依旧失效
以为是不能用class来用选择器，但是注释掉`.xxx`后是有效的

查阅了一下资料

**我们不能简单粗暴，理所当然的将上面的代码错误理解为“匹配父元素.main内第一个.box元素”**

即：匹配父元素.main内同类型标签元素div中的第一个元素.box，但我们看到.box 明显是父元素.main内同类型标签元素div中的第二个元素,所以.box元素（即第2个div标签）并没有被改变！

**解决方法**
使用nth-child(),或者nth-of-type()
