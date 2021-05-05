---
title: canvas实现画板（移动端+h5）
date: 2020-08-03 15:52:00
tags:
	- 前端
	- JavaScript
categories: 前端
---


代码如下：
```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="UTF-8" />
    <title>画板</title>
    <style>
        * {
            padding: 0;
            margin: 0;
        }

        body {
            background: #ccc;
        }

        canvas {
            background: #fff;
            margin: 50px 10px;
        }
    </style>
</head>

<body>
    <div style="text-align: center">
        <canvas height="600" width="900" id="canvas">
            <span>亲，您的浏览器不支持canvas，换个浏览器试试吧！</span>
        </canvas>
        <!-- <button>click me</button> -->
    </div>
    <script>
        window.onload = function () {
            var canvas = document.getElementById("canvas");
            var ctx = canvas.getContext("2d");
            let flag = true;
            ctx.lineWidth = 6.0;
            canvas.addEventListener('touchstart', (e) => {
                console.log("开始触摸！")
                let touch = e.targetTouches[0];
                let screen_x = e.targetTouches[0].clientX;
                let screen_y = e.targetTouches[0].clientY
                console.log(e)
                ctx.beginPath();
                ctx.moveTo(touch.clientX - canvas.offsetLeft, touch.clientY - canvas.offsetTop);
                canvas.addEventListener('touchmove', (e) => {
                    console.log("开始移动")
                    console.log(e)
                    if (e.targetTouches.length === 1) {
                        let touch = e.targetTouches[0];
                        //现在的坐标减去原来的坐标
                        ctx.lineTo(touch.clientX - canvas.offsetLeft, touch.clientY - canvas
                            .offsetTop);
                        ctx.stroke();
                    }

                }, false)
                canvas.addEventListener('touchend', (e) => {
                    ctx.closePath();
                    console.log("我没了")
                }, false)
            }, false)
            canvas.addEventListener("mousedown", (e) => {
                console.log("鼠标点击啦！")
                console.log(e)
                flag = false;
                ctx.beginPath();
                ctx.moveTo(e.clientX - canvas.offsetLeft, e.clientY - canvas.offsetTop);


            }, false)
            canvas.addEventListener("mousemove", (e) => {
                if (flag) {
                    return false;
                }
                console.log("我动啦")
                ctx.lineTo(e.clientX - canvas.offsetLeft, e.clientY - canvas.offsetTop);
                ctx.stroke();
            }, true)
            canvas.addEventListener("mouseup", (e) => {
                console.log("我没了")
                flag = true;
                e.stopPropagation();
                ctx.closePath();
            }, false)
        }
    </script>
</body>

</html>
```
实现效果：
1.网页版（h5端）
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200803154659896.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70)
2.移动端
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020080315474410.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70)

