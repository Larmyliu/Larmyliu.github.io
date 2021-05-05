最近学了canvas，写个小实例来巩固一下知识点
上一下效果图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200207160623300.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70)
运用到的函数（具体使用方法可以自行搜索相关文档）

 1. moveTo()
 2. lineTo()
 3. arc()
 4. quadraticCurveTo()
 5. bezierCurveTo()
 6. beginPath()
 7. fillStyle 
 8. stroke()
 9. fill()
 
 代码内容：
1.绘画头部
先利用arc()，画出外部的圆，再利用二次贝塞尔曲线画出面部轮廓
效果图：（白色多出的部分没关系，因为还要画项圈）![在这里插入图片描述](https://img-blog.csdnimg.cn/20200207154347215.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70)
代码：

```javascript
 		cxt.beginPath();//起始路径
        cxt.lineWidth = 1;//线宽度为1
        cxt.strokeStyle = '#000';//笔触的颜色
        cxt.arc(200,200,140,Math.PI*0.3,Math.PI*0.7,1);
        cxt.fillStyle = '#0bb0da';
        cxt.fill();
        cxt.stroke();
        cxt.beginPath();
        cxt.moveTo(150,320);
        cxt.quadraticCurveTo(20,205,150,140);
        cxt.lineTo(260,140);
        cxt.moveTo(260,140);
        cxt.quadraticCurveTo(380,205,260,320);
        cxt.lineTo(150,320);
        cxt.stroke();
        cxt.fillStyle = '#fff';
        cxt.closePath();
        cxt.fill();
```

2.画眼睛
主要利用到了arc(),和三次贝塞尔曲线
上图:
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200207154554288.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70)
代码

```javascript
		cxt.beginPath();
        cxt.moveTo(175,100);
        cxt.bezierCurveTo(140,100,140,180,175,180);
        cxt.bezierCurveTo(210,180,210,100,175,100);
        cxt.stroke();
        cxt.fillStyle = '#fff';
        cxt.fill();
        cxt.beginPath();
        cxt.moveTo(230,100);
        cxt.bezierCurveTo(195,100,195,180,230,180);
        cxt.bezierCurveTo(265,180,265,100,230,100);
        cxt.stroke();
        cxt.fillStyle = '#fff';
        cxt.fill();
        cxt.beginPath();
        cxt.arc(180,140,10,0,Math.PI*2);
        cxt.fillStyle = '#000';
        cxt.fill();
        cxt.beginPath();
        cxt.arc(225,140,10,0,Math.PI*2);
        cxt.fillStyle = '#000';
        cxt.fill();
```

3.画鼻子、胡须和嘴巴
用了lineTo()和二次方贝塞尔曲线
效果图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200207154840529.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70)
代码:

```javascript
		//画鼻子
        cxt.beginPath();
        cxt.arc(202,183,15,0,Math.PI*2);
        cxt.fillStyle = '#d05823';
        cxt.fill();
        //画嘴和胡须
        cxt.beginPath();
        cxt.moveTo(202,197);
        cxt.lineTo(202,295);
        cxt.stroke();
        cxt.beginPath();
        cxt.moveTo(180,210);
        cxt.lineTo(125,200);
        cxt.stroke();
        cxt.beginPath();
        cxt.moveTo(180,220);
        cxt.lineTo(125,220);
        cxt.stroke();
        cxt.beginPath();
        cxt.moveTo(180,230);
        cxt.lineTo(125,240);
        cxt.stroke();
        cxt.beginPath();
        cxt.moveTo(224,210);
        cxt.lineTo(280,200);
        cxt.stroke();
        cxt.beginPath();
        cxt.moveTo(224,220);
        cxt.lineTo(280,220);
        cxt.stroke();
        cxt.beginPath();
        cxt.moveTo(224,230);
        cxt.lineTo(280,240);
        cxt.stroke();
        cxt.beginPath();
        cxt.moveTo(132,270);
        cxt.quadraticCurveTo(202,320,272,270);
        cxt.stroke();
```
4.紧接着画围巾和铃铛
这里用了lineCap，arc()和lineTo()
效果图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020020715591553.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70)
代码

```javascript
 		 //画围巾
        cxt.beginPath();
        cxt.lineWidth='20';
        cxt.strokeStyle = '#d05823';
        cxt.lineCap ='round';
        cxt.moveTo(110,320);
        cxt.lineTo(285,320);
        cxt.stroke();
        //画铃铛
        cxt.beginPath();
        cxt.arc(202,330,15,0,Math.PI*2);
        cxt.fillStyle = 'yellow'
        cxt.fill();
        cxt.beginPath();
        cxt.arc(202,330,4,0,Math.PI*2);
        cxt.fillStyle = '#000';
        cxt.fill();
        cxt.beginPath();
        cxt.strokeStyle = '#000';
        cxt.lineWidth = '1'
        cxt.moveTo(187,330);
        cxt.lineTo(217,330);
        cxt.moveTo(202,330);
        cxt.lineTo(202,345);
        cxt.stroke();
```
5.画身体
就用了lineTo()，身体会把先画好的铃铛挡住了一部分，把铃铛的代码放在所以的后面即可
效果图:
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200207160025871.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70)
代码：

```javascript
//画身体
        cxt.beginPath();
        cxt.lineWidth='1';
        cxt.fillStyle = '#0bb0da';
        cxt.strokeStyle = '#000';
        cxt.moveTo(122,330);
        cxt.lineTo(80,370);
        cxt.lineTo(107,395);
        cxt.lineTo(122,380);
        cxt.lineTo(122,450);
        cxt.lineTo(272,450);
        cxt.lineTo(272,380);
        cxt.lineTo(287,395);
        cxt.lineTo(314,370);
        cxt.lineTo(277,330);
        cxt.closePath();
        cxt.fill();
        cxt.stroke();
```
6.画手
用arc()
效果图:
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200207160226973.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70)
代码：

```javascript
		//画手
        cxt.beginPath();
        cxt.arc(88,387,20,0,Math.PI*2);
        cxt.fillStyle = '#fff';
        cxt.fill();
        cxt.stroke();
        cxt.beginPath();
        cxt.arc(303,387,20,0,Math.PI*2);
        cxt.fillStyle = '#fff';
        cxt.fill();
        cxt.stroke();
```
7.画肚子和口袋
主要用三次方贝塞尔曲线
效果图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/202002071603567.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70)
代码：

```javascript
  		//画肚子
        cxt.beginPath();
        cxt.fillStyle = '#fff';
        cxt.moveTo(147,330)
        cxt.bezierCurveTo(97,460,307,460,252,330);
        cxt.fill();
        cxt.stroke();
        //画口袋
        cxt.beginPath();
        cxt.moveTo(165,380)
        cxt.bezierCurveTo(165,410,240,410,240,380);
        cxt.lineTo(165,380);
        cxt.stroke();
```
8.画脚和腿
用了arc()和二次
效果图：

代码：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200207160608934.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWFybXlsaXU=,size_16,color_FFFFFF,t_70)
```javascript
		//画左脚
        cxt.beginPath();
        cxt.strokeStyle = '#000';
        cxt.fillStyle = '#fff';
        cxt.lineCap ='round';
        cxt.moveTo(110,450);
        cxt.lineTo(190,450);
        cxt.quadraticCurveTo(200,455,190,470);
        cxt.lineTo(110,470);
        cxt.quadraticCurveTo(95,455,110,450);
        cxt.stroke();
        //画右脚
        cxt.beginPath();
        cxt.strokeStyle = '#000';
        cxt.fillStyle = '#fff';
        cxt.lineCap ='round';
        cxt.moveTo(205,450);
        cxt.lineTo(285,450);
        cxt.quadraticCurveTo(295,455,285,470);
        cxt.lineTo(205,470);
        cxt.quadraticCurveTo(200,455,205,450);
        cxt.stroke();
        //画腿
        cxt.beginPath();
        cxt.fillStyle = '#fff';
        cxt.arc(198,450,10,0,Math.PI,1);
        cxt.fill();
        cxt.stroke();
```
完整代码如下：

```javascript
 var cxt = document.getElementById('myCanvas').getContext('2d');
        //头部绘画  
        cxt.beginPath();//起始路径
        cxt.lineWidth = 1;//线宽度为1
        cxt.strokeStyle = '#000';//笔触的颜色
        cxt.arc(200,200,140,Math.PI*0.3,Math.PI*0.7,1);
        cxt.fillStyle = '#0bb0da';
        cxt.fill();
        cxt.stroke();
        cxt.beginPath();
        cxt.moveTo(150,320);
        cxt.quadraticCurveTo(20,205,150,140);
        cxt.lineTo(260,140);
        cxt.moveTo(260,140);
        cxt.quadraticCurveTo(380,205,260,320);
        cxt.lineTo(150,320);
        cxt.stroke();
        cxt.fillStyle = '#fff';
        cxt.closePath();
        cxt.fill();
        //眼睛绘画
        cxt.beginPath();
        cxt.moveTo(175,100);
        cxt.bezierCurveTo(140,100,140,180,175,180);
        cxt.bezierCurveTo(210,180,210,100,175,100);
        cxt.stroke();
        cxt.fillStyle = '#fff';
        cxt.fill();
        cxt.beginPath();
        cxt.moveTo(230,100);
        cxt.bezierCurveTo(195,100,195,180,230,180);
        cxt.bezierCurveTo(265,180,265,100,230,100);
        cxt.stroke();
        cxt.fillStyle = '#fff';
        cxt.fill();
        cxt.beginPath();
        cxt.arc(180,140,10,0,Math.PI*2);
        cxt.fillStyle = '#000';
        cxt.fill();
        cxt.beginPath();
        cxt.arc(225,140,10,0,Math.PI*2);
        cxt.fillStyle = '#000';
        cxt.fill();
        //画鼻子
        cxt.beginPath();
        cxt.arc(202,183,15,0,Math.PI*2);
        cxt.fillStyle = '#d05823';
        cxt.fill();
        //画嘴和胡须
        cxt.beginPath();
        cxt.moveTo(202,197);
        cxt.lineTo(202,295);
        cxt.stroke();
        cxt.beginPath();
        cxt.moveTo(180,210);
        cxt.lineTo(125,200);
        cxt.stroke();
        cxt.beginPath();
        cxt.moveTo(180,220);
        cxt.lineTo(125,220);
        cxt.stroke();
        cxt.beginPath();
        cxt.moveTo(180,230);
        cxt.lineTo(125,240);
        cxt.stroke();
        cxt.beginPath();
        cxt.moveTo(224,210);
        cxt.lineTo(280,200);
        cxt.stroke();
        cxt.beginPath();
        cxt.moveTo(224,220);
        cxt.lineTo(280,220);
        cxt.stroke();
        cxt.beginPath();
        cxt.moveTo(224,230);
        cxt.lineTo(280,240);
        cxt.stroke();
        cxt.beginPath();
        cxt.moveTo(132,270);
        cxt.quadraticCurveTo(202,320,272,270);
        cxt.stroke();
        //画围巾
        cxt.beginPath();
        cxt.lineWidth='20';
        cxt.strokeStyle = '#d05823';
        cxt.lineCap ='round';
        cxt.moveTo(110,320);
        cxt.lineTo(285,320);
        cxt.stroke();
        
        
        //画身体
        cxt.beginPath();
        cxt.lineWidth='1';
        cxt.fillStyle = '#0bb0da';
        cxt.strokeStyle = '#000';
        cxt.moveTo(122,330);
        cxt.lineTo(80,370);
        cxt.lineTo(107,395);
        cxt.lineTo(122,380);
        cxt.lineTo(122,450);
        cxt.lineTo(272,450);
        cxt.lineTo(272,380);
        cxt.lineTo(287,395);
        cxt.lineTo(314,370);
        cxt.lineTo(277,330);
        cxt.closePath();
        cxt.fill();
        cxt.stroke();
        //画手
        cxt.beginPath();
        cxt.arc(88,387,20,0,Math.PI*2);
        cxt.fillStyle = '#fff';
        cxt.fill();
        cxt.stroke();
        cxt.beginPath();
        cxt.arc(303,387,20,0,Math.PI*2);
        cxt.fillStyle = '#fff';
        cxt.fill();
        cxt.stroke();
        //画肚子
        cxt.beginPath();
        cxt.fillStyle = '#fff';
        cxt.moveTo(147,330)
        cxt.bezierCurveTo(97,460,307,460,252,330);
        cxt.fill();
        cxt.stroke();
        //画口袋
        cxt.beginPath();
        cxt.moveTo(165,380)
        cxt.bezierCurveTo(165,410,240,410,240,380);
        cxt.lineTo(165,380);
        cxt.stroke();
        //画左脚
        cxt.beginPath();
        cxt.strokeStyle = '#000';
        cxt.fillStyle = '#fff';
        cxt.lineCap ='round';
        cxt.moveTo(110,450);
        cxt.lineTo(190,450);
        cxt.quadraticCurveTo(200,455,190,470);
        cxt.lineTo(110,470);
        cxt.quadraticCurveTo(95,455,110,450);
        cxt.stroke();
        //画右脚
        cxt.beginPath();
        cxt.strokeStyle = '#000';
        cxt.fillStyle = '#fff';
        cxt.lineCap ='round';
        cxt.moveTo(205,450);
        cxt.lineTo(285,450);
        cxt.quadraticCurveTo(295,455,285,470);
        cxt.lineTo(205,470);
        cxt.quadraticCurveTo(200,455,205,450);
        cxt.stroke();
        //画腿
        cxt.beginPath();
        cxt.fillStyle = '#fff';
        cxt.arc(198,450,10,0,Math.PI,1);
        cxt.fill();
        cxt.stroke();
        //画铃铛
        cxt.beginPath();
        cxt.arc(202,330,15,0,Math.PI*2);
        cxt.fillStyle = 'yellow'
        cxt.fill();
        cxt.beginPath();
        cxt.arc(202,330,4,0,Math.PI*2);
        cxt.fillStyle = '#000';
        cxt.fill();
        cxt.beginPath();
        cxt.strokeStyle = '#000';
        cxt.lineWidth = '1'
        cxt.moveTo(187,330);
        cxt.lineTo(217,330);
        cxt.moveTo(202,330);
        cxt.lineTo(202,345);
        cxt.stroke();
```

