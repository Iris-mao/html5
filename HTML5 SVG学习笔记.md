####SVG API：
* SVG是一种可缩放矢量图形，一种二维图形表示语言
* 与canvas不同的是，在浏览器的开发工具中能查看和编辑SVG结构，SVG嵌入到HTML DOM中了
* 缩放SVG不会导致图片的质量下降，图片不会模糊
* SVG相关词汇：形状、路径、变换、图案和渐变、可重用的内容、文本
* 创建SVG
```html
<svg height=100 width=500></svg>
```
* 简单的形状的绘制
```html
<svg height=200 width=200>
    <!--Stroke属性定义一条线，文本或元素轮廓颜色 stroke- width属性定义了一条线，文本或元素轮廓厚度 fill属性是填充颜色 cx、cy是椭圆的原点坐标-->
    <rect height="80" width="100" x="10" y="20" stroke="red" fill="#ccc"></rect>
    <circle cx="120" cy="80" r="40" stroke="#00f" fill="none" stroke-width="8"></circle>
</svg>
```
* 可以将要变换的SVG元素放在一个组中，添加变换属性进行变换，组内成员可以通过id来引用
```html
<svg height=200 width=200>
    <!--translate输入一个或两个值得来声明水平和竖直移动值。tx代表x轴上的translation值；ty代表y轴上的translation值
        rotate对于给定的点和旋转角度值执行旋转,默认单位是度
        scale输入一个或两个值来声明水平和竖直缩放值。sx代表沿x轴的缩放值，用来水平延长或者拉伸元素；sy代表沿y轴缩放值，用来垂直延长或者缩放元素-->
    <g transform="translate(60,0) rotate(30) scale(0.75)" id="ShapeGroup">
        <rect height="80" width="100" x="10" y="20" stroke="red" fill="#ccc"></rect>
        <circle cx="120" cy="80" r="40" stroke="#00f" fill="none" stroke-width="8"></circle>
    </g>
</svg>
```
* 复用内容,`<defs>`元素用于定义下面要使用的内容,`<use`元素用于链接到`<defs>`元素定义的内容
```HTML
<svg height=200 width=200>
    <defs>
        <g id="ShapeGroup">
            <rect height="80" width="100" x="10" y="20" stroke="red" fill="#ccc"></rect>
            <circle cx="120" cy="80" r="40" stroke="#00f" fill="none" stroke-width="8"></circle>
        </g>
    </defs>
    <use xlink:href="#ShapeGroup" transform="translate(60,0) scale(0.5)"/>
    <use xlink:href="#ShapeGroup" transform="translate(120,80) scale(0.4)"/>
    <use xlink:href="#ShapeGroup" transform="translate(20,60) scale(0.25)"/>
</svg>
```
* 图案和渐变（图案可以像素图形或者SVG图形，渐变分为线性渐变和放射性渐变）
```html
<svg  height=200 width=200>
    <defs>
        <!--<pattern>是SVG的一个图案填充标签，可以在pattern中定义好图案，然后通过id引用来对某个图形进行填充
            userSpaceOnUse：x、y、width和height表示的值都是当前用户坐标系统的值。也就是说，这些值没有缩放，都是绝对值。
            viewBox="x, y, width, height"  // x:左上角横坐标，y:左上角纵坐标，width:宽度，height:高度,viewBox是“视区盒子”的意思-->
        <pattern id="GravelPattern" patternUnits="userSpaceOnUse"
               x="0" y="0" width="100" height="67" viewBox="0 0 100 67">
            <image height="67" xlink:href="gravel.jpg" width="100"></image>
        </pattern>

        <linearGradient id="RedBlackGradient">
            <stop offset="0%" stop-color="#000"></stop>
            <stop offset="100%" stop-color="#f00"></stop>
        </linearGradient>
    </defs>

    <rect height="100" width="80" x="10" y="20" stroke="red" fill="url(#RedBlackGradient)"></rect>
    <circle r="40" cx="120" cy="80" stroke="#00f" stroke-width="8" fill="url(#GravelPattern)"></circle>
</svg>
```
* SVG路径,path有一个D属性，M代表moveTo，L代表lineTo，Q代表二次曲线，Z代表闭合路径
```html
<svg height=1000 width=1000>
    <g transform="translate(50,350)" stroke-width="20" stroke="url(#GravelPattern)" stroke-linejoin="round">
        <path d="M-25,-50
                 L-10,-80
                 L-20,-80
                 L-5,-110
                 L-15,-110
                 L0,-140
                 L15,-110
                 L5,-110
                 L20,-80
                 L10,-80
                 L25,-50
                 Z" id="Canopy"></path>
        <path d="M0,0 Q170,-50 260,-190 Q310,-250 410,-250" fill="none"></path>
    </g>
</svg>
```
* SVG文本，在浏览器中，文本是可选的，并且能够被搜索引擎搜到
```html
<svg height=200 width=500>
    <text y="60" x="300" font-family="impact" font-size="60px" fill="#996600" text-anchor="middle">Happy Trails</text>
</svg>
```
* 组合场景（将上述功能组合成一个跑道场景）
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Happy Trails in SVG</title>
    <style>
        svg {
            border: 1px solid black;
        }
    </style>
</head>
<body>
<svg width="400" height="600">
    <defs>
        <pattern id="GravelPattern" patternUnits="userSpaceOnUse"
                 x="0" y="0" width="100" height="67" viewBox="0 0 100 67">
            <image height="67" xlink:href="gravel.jpg" width="100"></image>
        </pattern>
        <linearGradient id="TrunkGradient">
            <stop offset="0%" stop-color="#663300"></stop>
            <stop offset="40%" stop-color="#996600"></stop>
            <stop offset="100%" stop-color="#552200"></stop>
        </linearGradient>
        <rect height="50" width="10" x="-5" y="-50" id="Trunk"></rect>
        <path d="M-25,-50
                 L-10,-80
                 L-20,-80
                 L-5,-110
                 L-15,-110
                 L0,-140
                 L15,-110
                 L5,-110
                 L20,-80
                 L10,-80
                 L25,-50
                 Z" id="Canopy"></path>
        <linearGradient id="CanopyShadow" x="0" y="0" x2="0" y2="100%">
            <stop offset="0%" stop-color="#000" stop-opacity=".5"></stop>
            <stop offset="20%" stop-color="#000" stop-opacity="0"></stop>
        </linearGradient>
        <g id="Tree">
            <use xlink:href="#Trunk" fill="url(#TrunkGradient)"/>
            <use xlink:href="#Trunk" fill="url(#CanopyShadow)"/>
            <use xlink:href="#Canopy" fill="none" stroke="#663300" stroke-linejoin="round" stroke-width="4px"/>
            <use xlink:href="#Canopy" fill="#339900" stroke="none"/>
        </g>
        <g id="TreeShadow">
            <use xlink:href="#Trunk" fill="#000"/>
            <use xlink:href="#Canopy" fill="#000" stroke="none"/>
        </g>
    </defs>

    <g transform="translate(-10,350)" stroke-width="20" stroke="url(#GravelPattern)" stroke-linejoin="round">
        <path d="M0,0 Q170,-50 260,-190 Q310,-250 410,-250" fill="none"></path>
    </g>

    <text y="60" x="200" font-family="impact" font-size="60px" fill="#996600" text-anchor="middle">Happy Trails!</text>
    <use xlink:href="#TreeShadow" transform="translate(130,250) scale(1,.6) skewX(-18)" opacity="0.4"/>
    <use xlink:href="#Tree" transform="translate(130,250)"/>
    <use xlink:href="#TreeShadow" transform="translate(260,500) scale(2,1.2) skewX(-18)" opacity="0.4"/>
    <use xlink:href="#Tree" transform="translate(260,500) scale(2)"/>
</svg>
</body>
```
* 使用SVG创建交互式应用，添加一个按钮，每添加一个元素其实就是添加一个`<use>`元素
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Happy Trails in SVG</title>
    <style>
        svg {
            border: 1px solid black;
        }

        g[id=Tree]:hover {
            opacity: 0.9;
            cursor: crosshair;
        }
    </style>
</head>
<body>
<div>
    <button id="AddTreeButton">Add Tree</button>
</div>
<svg width="400" height="600">
    <defs>
        <pattern id="GravelPattern" patternUnits="userSpaceOnUse"
                 x="0" y="0" width="100" height="67" viewBox="0 0 100 67">
            <image height="67" xlink:href="gravel.jpg" width="100"></image>
        </pattern>
        <linearGradient id="TrunkGradient">
            <stop offset="0%" stop-color="#663300"></stop>
            <stop offset="40%" stop-color="#996600"></stop>
            <stop offset="100%" stop-color="#552200"></stop>
        </linearGradient>
        <rect height="50" width="10" x="-5" y="-50" id="Trunk"></rect>
        <path d="M-25,-50
                 L-10,-80
                 L-20,-80
                 L-5,-110
                 L-15,-110
                 L0,-140
                 L15,-110
                 L5,-110
                 L20,-80
                 L10,-80
                 L25,-50
                 Z" id="Canopy"></path>
        <linearGradient id="CanopyShadow" x="0" y="0" x2="0" y2="100%">
            <stop offset="0%" stop-color="#000" stop-opacity=".5"></stop>
            <stop offset="20%" stop-color="#000" stop-opacity="0"></stop>
        </linearGradient>
        <g id="Tree">
            <use xlink:href="#Trunk" fill="url(#TrunkGradient)"/>
            <use xlink:href="#Trunk" fill="url(#CanopyShadow)"/>
            <use xlink:href="#Canopy" fill="none" stroke="#663300" stroke-linejoin="round" stroke-width="4px"/>
            <use xlink:href="#Canopy" fill="#339900" stroke="none"/>
        </g>
    </defs>

    <g transform="translate(-10,350)" stroke-width="20" stroke="url(#GravelPattern)" stroke-linejoin="round">
        <path d="M0,0 Q170,-50 260,-190 Q310,-250 410,-250" fill="none"></path>
    </g>

    <text y="60" x="200" font-family="impact" font-size="60px" fill="#996600" text-anchor="middle">Happy Trails!</text>
    <text y="90" x="200" font-family="impact" font-size="20px" fill="#996600" text-anchor="middle"
          id="TreeCounter"></text>
    <text y="420" x="20" font-family="impact" font-size="20px" fill="#996600" text-anchor="left">
        <tspan>You can remove a</tspan>
        <tspan>tree by clicking on it.</tspan>
    </text>
    <use xlink:href="#Tree" transform="translate(130,250)"/>
    <use xlink:href="#Tree" transform="translate(260,500) scale(2)"/>
</svg>
</body>
<script>
    function removeTree(e) {
        var elt = e.target;
        if (elt.correspondingUseElement) {
            elt = elt.correspondingUseElement;   //此段代码目前不知道用意何在
        }
        elt.parentNode.removeChild(elt);
        updateTrees();
    }

    document.getElementById("AddTreeButton").onclick = function () {
        var x = Math.floor(Math.random() * 400);  //产生新生成数的X,Y坐标
        var y = Math.floor(Math.random() * 600);
        var scale = Math.random() + .5;   //产生新生成数的缩放比例
        var translate = "translate(" + x + "," + y + ")";

        var tree = document.createElementNS("http://www.w3.org/2000/svg", "use");   //createElementNS() 方法可创建带有指定命名空间的元素节点
        tree.setAttributeNS("http://www.w3.org/1999/xlink", "xlink:href", "#Tree");
        tree.setAttribute("transform", translate + "scale(" + scale + ")");
        document.querySelector("svg").appendChild(tree);  //querySelector() 方法返回文档中匹配指定 CSS 选择器的一个元素
        updateTrees();
    };

    function updateTrees() {
        var list = document.querySelectorAll("use");  //querySelectorAll 在文档内找全部符合选择器描述的节点包括Element本身
        var treeCount = 0;
        for (var i = 0; i < list.length; i++) {
            if (list[i].getAttribute("xlink:href") == "#Tree") {
                treeCount++;
                list[i].onclick = removeTree;
            }
        }
        var counter = document.getElementById("TreeCounter");
        counter.textContent=treeCount+"trees in forest";  //textContent 属性设置或返回指定节点的文本内容，以及它的所有后代
    }
    updateTrees();
</script>
```