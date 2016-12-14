####Canvas API
* 在网页上使用cnavas元素时，会创建一块矩形区域，默认大小是300*150px
* 坐标从左上角开始的，x轴沿着水平方向向右延伸，y轴垂直方向向下延伸
* 检测浏览器对canvas的支持情况:
  ```html
  <!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Canvas API</title>
</head>
<body>
<div id="support"></div>
</body>
<script>
    try{
        document.createElement("canvas").getContext("2d");
        document.getElementById("support").innerHTML="HTML5 Canvas is supported in you browser.";
    }catch (e){
        document.getElementById("support").innerHTML="HTML5 Canvas is not supported in you browser.";
    }
</script>
</html>
  ```
* 绘制简单的对角线：(对上下文的操作不会立即反应在页面上，只有对路径应用绘制（stroke）或填充（fill）方法时，结果才会显示出来)
  以直接绘制的方式来绘制
```html
   <!DOCTYPE html>
   <html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Canvas API</title>
</head>
<body>
    <canvas id="diagonal" style="border: 1px solid;" height="200" width="200"></canvas>
</body>
<script>
    function drawDiagonal() {
        //取得canvas元素及其绘图上下文
        var canvas = document.getElementById('diagonal');
        var context = canvas.getContext('2d');

        //用绝对坐标来创建一条路径
        context.beginPath();
        context.moveTo(70,140);
        context.lineTo(140,70);

        //将这条线绘制到canvas上
        context.stroke();
    }

    window.addEventListener("load",drawDiagonal,true);
</script>
</html>
```
   以转换的方法进行绘制(translate() 方法重新映射画布上的 (0,0) 位置。)
   ```html
   //保存你当前绘图状态
        context.save();

        //向右下方移动绘图上下文
        context.translate(70,140);

        //以原点为起点，绘制与前面相同的线段
        context.beginPath();
        context.moveTo(0,0);
        context.lineTo(70,-70);
        context.stroke();

        //恢复原有的绘图状态
        context.restore();
   ```
* 下面以绘制带树的跑道为例，写一段完成的程序
1. html部分代码
```html
<canvas id="trails" style="border: 1px solid;" height="500" width="400"></canvas>
```
2. JS部分代码
```javascrip
 //加载砾石背景图片
    var gravel = new Image();
    gravel.src = "gravel.jpg";
    gravel.onload = function () {
        drawTrails();
    };

    function createCanopyPath(context) {
        //绘制树冠
        context.beginPath();
        context.moveTo(-25, -50);
        context.lineTo(-10, -80);
        context.lineTo(-20, -80);
        context.lineTo(-5, -110);
        context.lineTo(-15, -110);

        //树的顶点
        context.lineTo(0, -140);
        context.lineTo(15, -110);
        context.lineTo(5, -110);
        context.lineTo(20, -80);
        context.lineTo(10, -80);
        context.lineTo(25, -50);

        //连接起点，闭合路径
        context.closePath();
    }

    function drawTree(context) {
        //给树创建树影
        context.save();
        //x值随着Y值得增加而增加，借助拉伸变换，可以创建一棵用作阴影的倾斜的树，应用了变换以后，所有坐标都与矩阵相乘
        context.transform(1, 0, -0.5, 1, 0, 0);

        //在Y轴方向，将阴影的高度压缩为原来的60%
        context.scale(1, 0.6);

        //使用透明度为20%的黑色填充树干
        context.fillStyle = 'rgba(0,0,0,0.2)';
        context.fillRect(-5, -50, 10, 50);
        //使用已有的阴影效果重新绘制树
        createCanopyPath(context);
        context.fill();

        //恢复之前的canvas状态
        context.restore();
        createCanopyPath(context);

        //创建用作树干纹理的三阶水平渐变
        var trunkGradient = context.createLinearGradient(-5, -50, 5, -50);

        //树干的左侧边缘是一般程度的棕色
        trunkGradient.addColorStop(0, '#663300');

        //树干中间偏左的位置颜色要淡一些
        trunkGradient.addColorStop(0.4, '#996600');

        //树干右侧边缘的颜色要深一些
        trunkGradient.addColorStop(1, '#552200');

        //使用渐变色填充树干
        context.fillStyle = trunkGradient;
        context.fillRect(-5, -50, 10, 50);

        //接下来，创建垂直渐变，用以作树冠在树干上投影
        var canopyShadow = context.createLinearGradient(0, -50, 0, 0);

        //投影渐变的起点是透明度设为50%的黑色
        canopyShadow.addColorStop(0.2, 'rgba(0,0,0,0.5)');

        //方向垂直向下，渐变色在很短的距离内迅速渐变至完全透明，这段长度之外的树干没有投影
        canopyShadow.addColorStop(0.2, 'rgba(0,0,0,0.0)');

        //在树干上填充投影渐变
        context.fillStyle = canopyShadow;
        context.fillRect(-5, -50, 10, 50);

        //加宽线条
        context.lineWidth = 4;

        //平滑路径的接合点 round/bevel/miter
        context.lineJoin = 'round';

        //将颜色改为棕色
        context.strokeStyle = '#663300';
        context.stroke();

        //将填充色设置为绿色并填充树冠(填充是在绘图之前进行的，所以不会覆盖线条宽度，如果在绘图之后填充，就会覆盖线条宽度)
        context.fillStyle = "#339900";
        context.fill();
    }

    function createCurvePath(context) {

        //保存canvas的状态并绘制路径
        context.save();

        context.translate(-10, 350);
        context.beginPath();

        //第一条曲线向右上方弯曲(第一组xy坐标是曲线的控制点，第二组xy坐标是曲线的终点)
        context.moveTo(0, 0);
        context.quadraticCurveTo(170, -50, 260, -190);

        //第二条曲线向右下方弯曲
        context.quadraticCurveTo(310, -250, 410, -250);

        //用背景图替代棕色粗线条
        context.strokeStyle = context.createPattern(gravel, 'repeat');

        //使用棕色的粗线条来绘制路径
//        context.strokeStyle='#663300';
        context.lineWidth = 20;
        context.stroke();

        //恢复之前的canvas状态
        context.restore();
    }

    function drawTrails() {
        var canvas = document.getElementById('trails');
        var context = canvas.getContext('2d');
        //在（130，250）的位置绘制第一颗树
        context.save();
        context.translate(130, 250);
        drawTree(context);
        //绘制当前路径
//        context.stroke();
        context.restore();

        //在（260，500）的位置绘制第二颗树
        context.save();
        context.translate(260, 500);

        //第二颗树的宽高分别放大至原来的2倍
        context.scale(2, 2);
        drawTree(context);
        context.restore();

        //绘制小路
        createCurvePath(context);

        //在canvas上绘制标题文本
        context.save();

        //字号为60px,字体为impact
        context.font = "60px impact";

        //将文本填充为棕色
        context.fillStyle = '#996600';
        //将文本设置为居中对齐
        context.textAlign = 'center';
        //设置文字阴影的颜色为黑色，透明度为20%
        context.shadowColor = 'rgba(0,0,0,0.2)';

        //将阴影向右移动15px，向上移动10px
        context.shadowOffsetX = 15;
        context.shadowOffsetY = -10;

        //轻微模糊阴影
        context.shadowBlur = 2;

        //在canvas顶部中央的位置以大字体的形式显示文本
        context.fillText('Happy Trails!', 200, 60, 400);
        context.restore();
    }
```
3. 附示例程序中用到的图片
4. 技术点分解
* 树冠的绘制是通过moveTo lineTo的方法，在原点进行绘制，然后用translate方法移动位置
  样式的修改：lineWidth（线宽） lineJoin（连接点的平滑度） strokeStyle（颜色） lineCap（线条末端）
* 曲线的绘制用quadraticCurveTo方法，第一组参数是弯曲点，第二组参数是终点
* 渐变的制作分三步：1、创建渐变对象  trunkGradient       
  2、为渐对象设置颜色，指明过渡方式 addColorStop
  3、在context上为填充样式或描边样式设置渐变 fillStyle
  4、rgba(R,G,B,A)，最后一个是Alph通道透明度
* 背景图的添加用 createPattern(img,'repeat/repeat-x/repear-y/no-repeat')
* 缩放 scale
* 制作阴影 transfom(a,b,c,d,e,f)  要了解矩阵的概念
* Canvas文本 fillText  strokeText
* 应用阴影：shadowColor（颜色） shadowOffsetX（向右阴影像素值） shadowOffsetY（向下阴影像素值） shadowBlur（模糊值）