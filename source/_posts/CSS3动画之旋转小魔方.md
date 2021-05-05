利用CSS3动画特性的小例子

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>
        .wapper{
        
            width: 300px;
            height: 300px;
            
        }
        .box{
            position: absolute;
            width: 200px;
            height: 200px;
            top: 150px;
            left: 50%;
            animation: move 5s linear infinite;
            margin: 0 auto;
            transform-style: preserve-3d;
            backface-visibility: hidden;
            transform-origin: center;
        }
        .box div{
            position: absolute;
            top: 100px;
            display: inline-block;
            width: 100px;
            height: 100px;
            font-size: 16px;
            text-align: center;
            line-height: 100px;
            opacity: 0.7;
        }
        .box div:nth-of-type(1){
            background: red;
            left: 0px;
            transform: rotateY(90deg) translateZ(50px) translateX(50px);
        }
        .box div:nth-of-type(2){
            background: yellow;
            left: 100px;
        }
        .box div:nth-of-type(3){
            background: blue;
            left: 200px;
            transform:  rotateY(-90deg) translateZ(50px) translateX(-50px);
        }
        .box div:nth-of-type(4){
            background: green;
            left: 300px;
            transform: translateX(-200px) translateZ(-100px);
        }
        .box div:nth-of-type(5){
            position: absolute;
            background: purple;
            top: 0px;
            left: 100px;
            transform: rotateX(90deg) translateZ(-50px) translateY(-50px)
        }
        .box div:nth-of-type(6){
            top: 200px;
            left: 100px;
            background: darkkhaki;
            transform: rotateX(-90deg) translateZ(-50px) translateY(50px);
        }
        @keyframes move{
            0%{
                transform: rotateX(0deg) rotateY(0deg);
            }
            100%{
                transform: rotateX(360deg) rotateY(360deg);
            }
        }
    </style>
</head>
<body>
    <div class="wapper">
        <div class="box">
            <div>1</div>
            <div>2</div>
            <div>3</div>
            <div>4</div>
            <div>5</div>
            <div>6</div>
        </div>
    </div>
    
</body>
</html>
```
通过折叠变换，3d操作，把6个正方形拼成魔方
总结一下在2d和3d中坐标轴的方向来判断translate和rotate的方向
坐标轴横X竖Y面朝自己是Z轴
在2d中translateZ和translateX方向是一样的
