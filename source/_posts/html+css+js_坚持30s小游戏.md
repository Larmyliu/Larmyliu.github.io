## 坚持30s小游戏
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200522150652674.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70)
用到：

 1. 弹性运动
 2. 拖拽事件
 3. 碰撞检测
 4. 定时器清除
 5. 单对象编程

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" href="./css/consist30s.css">
</head>
<body>
    <span>游戏马上开始</span>
    <div class="outer">
        <!-- <div class="green"></div> -->
        <div class="red"></div>
    </div>
    <p>游戏规则：坚持30s不死</p>
    <script src="./js/consist30s.js"></script>
</body>
</html>
```

```css
*{
    padding: 0;
    margin: 0;
    list-style: none;
}
body{
    padding: 30px;
    background: #326b86;
    color: #fff;
    font-size: 16px;
    font-weight: 600;
    text-align: center;
}
.outer{
    position: relative;
    width: 450px;
    height: 450px;
    background: #222;
    margin: 20px auto;
}
.red{
    position: absolute;
    top: 195px;
    left: 195px;
    width: 60px;
    height: 60px;
    background: red;
    opacity: .5;
    border-radius: 50%;
}
.green{
    position: absolute;
    width: 50px;
    height: 50px;
    background: green;
    border-radius: 50%;
    opacity: .5;
}
```

```javascript
/**
 * 1.随机生成绿色小球在顶部
 * 
 * 2.绿球会产生运动
 * 
 * 3.弹性运动
 * 
 * 4.生成多个小绿球
 * 
 * 5.红球拖拽
 * 
 * 6.红球和绿球之间的碰撞检测
 * 
 * 7.定时器清除
 * 
 * 8.单对象编程
 */

var game = {
    //获得红球
    redBall:document.getElementsByClassName('red')[0],
    greenArr:[],
    flag: true,
    num:0,
    numRun: document.getElementsByTagName('span')[0],
    movePlus:{
        outer:document.getElementsByClassName('outer')[0],//舞台
        iWidth:document.getElementsByClassName('outer')[0].offsetWidth,//舞台宽
        iHeight:document.getElementsByClassName('outer')[0].offsetHeight,//舞台高
        ispeedX:10,//绿球速度x
        ispeedY:10,//绿球速度y
    },
    init:function() {
        console.log('游戏开始');
        this.createBall(this.movePlus) //创建小绿球
        this.dragRedBall(this.movePlus);//红球拖拽
        this.timeRun();//开始计时
        
    },
    timeRun:function () {
        var self = this;
        this.timer = setInterval(function () {
            if(self.num >= 30){
                alert('真男人')
                self.clearTimer()
            }
            self.num++;
            self.numRun.innerHTML = '坚持了' + self.num + '秒';
        },1000)
    },
    createBall:function(obj) {
        var plus = obj;
        var self = this;
        function Green(plus) { //构造函数
            this.ball = document.createElement('div');//创建绿球
            this.ball.className = 'green';
            plus.outer.appendChild(this.ball);//添加进舞台
            this.subWidth = Math.floor(Math.random()*(plus.iWidth - this.ball.offsetWidth));
            console.log(this.subWidth)
            this.ball.style.left = this.subWidth + 'px';
            this.speedX = Math.floor(Math.random()*plus.ispeedX) + 1;
            this.speedY = Math.floor(Math.random()*plus.ispeedY) + 1;
            this.iWidth = plus.iWidth;
            this.iHeight = plus.iHeight;
        }
        var green = new Green(plus);
        this.greenArr.push(green);
        this.createTimer = setInterval(function () {
            var green = new Green(plus);
            self.greenArr.push(green)
        },2000)
        this.moveBall();
    },
    moveBall:function() {
        var self = this;
        this.goTimer = setInterval(function() {
            for(var i = 0; i < self.greenArr.length; i++){
                self.crashCheck(self.greenArr[i])//碰撞检测
                var newLeft = self.greenArr[i].ball.offsetLeft +  self.greenArr[i].speedX;
                var newTop =  self.greenArr[i].ball.offsetTop +  self.greenArr[i].speedY;
                //弹性运动
                self.greenArr[i].ball.style.left = newLeft + 'px';
                self.greenArr[i].ball.style.top = newTop + 'px';
                if(newLeft < 0) {
                    self.greenArr[i].speedX *=-1;
                }else if (newLeft > self.greenArr[i].iWidth - self.greenArr[i].ball.offsetWidth){
                    self.greenArr[i].speedX *=-1;
                }else if (newTop < 0){
                    self.greenArr[i].speedY *=-1;
                }else if (newTop > self.greenArr[i].iHeight - self.greenArr[i].ball.offsetHeight){
                    self.greenArr[i].speedY *=-1;
                }
            }
            
        }, 50)
    },
    dragRedBall:function(obj) {
        var self = this;
        this.redBall.onmousedown = function(e) {
                var e_x = e.pageX;
                var e_y = e.pageY;
            document.onmousemove = function(e) {
                var chax = e.pageX - e_x;
                var chay = e.pageY - e_y;
                self.redBall.style.left = chax + self.redBall.offsetLeft + 'px';
                self.redBall.style.top = chay + self.redBall.offsetTop + 'px';
                //更新上一个点，产生差值
                e_x = e.pageX;
                e_y = e.pageY;
                //判断边界
                if(self.redBall.offsetLeft < 0  && self.flag){
                    alert('Game Over!!');
                    self.flag = false;
                    self.clearTimer();
                    window.location.reload();
                    
                }else if (self.redBall.offsetLeft > (obj.iWidth - self.redBall.offsetWidth) && self.flag){
                    alert('Game Over!!');
                    self.flag = false;
                    self.clearTimer();
                    window.location.reload();
                }else if (self.redBall.offsetTop < 0 && self.flag) {
                    alert('Game Over!!');
                    self.flag = false;
                    self.clearTimer();
                    window.location.reload();
                }else if(self.redBall.offsetTop > (obj.iHeight - self.redBall.offsetHeight) && self.flag){
                    console.log(self.redBall.offsetTop);
                    console.log(obj.iHeight,self.redBall.offsetTop)
                    alert('Game Over!!');
                    self.flag = false;
                    self.clearTimer();
                    window.location.reload();
                }
            }
            this.onmouseup = function(e) {
                document.onmousemove = null;
            }
        }
        
    },
    crashCheck:function (green) {

        this.redBall;
        //绿球圆心点
        var greenX1 = green.ball.offsetLeft + Math.floor(green.ball.offsetWidth/2);
        var greenY1 = green.ball.offsetTop + Math.floor(green.ball.offsetHeight/2);
        //红球圆心
        var redX2 = this.redBall.offsetLeft + Math.floor(this.redBall.offsetWidth/2);
        var redY2 = this.redBall.offsetTop + Math.floor(this.redBall.offsetHeight/2);
        // x1-x2
        var dx = Math.abs(greenX1 - redX2);
        var dy = Math.abs(greenY1 - redY2);
        //两点距离
        var distance = Math.floor(Math.sqrt(Math.pow(dx, 2) + Math.pow(dy, 2)));
        console.log(distance)
        var R = this.redBall.offsetWidth/2 + green.ball.offsetWidth/2;
        if(distance < R && this.flag){
            alert('Game Over!!');
            this.flag = false;
            this.clearTimer();
            window.location.reload();
        }
    },
    clearTimer:function () {
        clearInterval(this.timer);
        clearInterval(this.createTimer);
        clearInterval(this.goTimer);
    }

}
game.init();
```
弹性运动：
在绿色小球碰到四个壁时要方向要发生改变，运动方向变成原来的-1
拖拽事件：鼠标点下去时的点，和移动过程中产生的新点之间的差值就是移动的距离，移动过程中要不断更新上一个点的值，保证持续产生差值；
碰撞检测：
当两个点的中心距离小于两个球的半径之和即为碰撞
最后游戏结束后要把所有的定时器给清除

