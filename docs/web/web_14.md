# 第 1 节 【情人节福利】JS 实现玫瑰花

*   原创来自于博客 http://www.romancortes.com/blog/1k-rose/

*   中文版翻译于 http://www.csdn.net/article/2012-02-09/311621 有增改

## 前言

先放压缩过的 html 代码，把下段代码复制到你的新建的 html 文件用浏览器打开就能看到一个玫瑰。我们管它叫生产版好了，三步就能完成。

在文档的最后附上一个简单的开发版本，供大家定制 :-)

实验楼的虚拟机**共享桌面**就能让你喜欢的人看到哟~

![enter image description here](img/306.jpg)

**共享桌面**使用方法请猛戳[这里](http://www.shiyanlou.com/faq#sharedesktop)

情人节这么浪漫的告白，快去试试吧，送她或者他一朵**与众不同的玫瑰花**

## 代码（生产版）

```
<!DOCTYPE HTML>
<html>  
<head>  
<title>Rose</title>  
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">  
</head>  
<body style="margin-left:350px">  
<canvas id="c"></canvas>  
<script>  
    var b = document.body;  
    var c = document.getElementsByTagName('canvas')[0];  
    var a = c.getContext('2d');  
    document.body.clientWidth;  
</script>  
<script>  

with(m=Math)C=cos,S=sin,P=pow,R=random;c.width=c.height=f=600;h=-250;function p(a,b,c){if(c>60)return[S(a*7)*(13+5/(.2+P(b*4,4)))-S(b)*50,b*f+50,625+C(a*7)*(13+5/(.2+P(b*4,4)))+b*400,a*1-b/2,a];A=a*2-1;B=b*2-1;if(A*A+B*B<1){if(c>37){n=(j=c&1)?6:4;o=.5/(a+.01)+C(b*125)*3-a*300;w=b*h;return[o*C(n)+w*S(n)+j*610-390,o*S(n)-w*C(n)+550-j*350,1180+C(B+A)*99-j*300,.4-a*.1+P(1-B*B,-h*6)*.15-a*b*.4+C(a+b)/5+P(C((o*(a+1)+(B>0?w:-w))/25),30)*.1*(1-B*B),o/1e3+.7-o*w*3e-6]}if(c>32){c=c*1.16-.15;o=a*45-20;w=b*b*h;z=o*S(c)+w*C(c)+620;return[o*C(c)-w*S(c),28+C(B*.5)*99-b*b*b*60-z/2-h,z,(b*b*.3+P((1-(A*A)),7)*.15+.3)*b,b*.7]}o=A*(2-b)*(80-c*2);w=99-C(A)*120-C(b)*(-h-c*4.9)+C(P(1-b,7))*50+c*2;z=o*S(c)+w*C(c)+700;return[o*C(c)-w*S(c),B*99-C(P(b, 7))*50-c/3-z/1.35+450,z,(1-b/1.2)*.9+a*.1, P((1-b),20)/4+.05]}}setInterval('for(i=0;i<1e4;i++)if(s=p(R(),R(),i%46/.74)){z=s[2];x=~~(s[0]*f/z-h);y=~~(s[1]*f/z-h);if(!m[q=y*f+x]|m[q]>z)m[q]=z,a.fillStyle="rgb("+~(s[3]*h)+","+~(s[4]*h)+","+~(s[3]*s[3]*-80)+")",a.fillRect(x,y,1,1)}',0)  

</script>  
</body>  
</html> 
```

使用了多个不同的形状图来组成这朵代码玫瑰。共使用了 31 个形状：24 个花瓣，4 个萼片，2 个叶子和 1 根花茎，其中每一个形状图都用代码进行描绘。

首先，来定义一个采样范围：

```
<script>
function surface(a, b) { 
    // 使用 a 和 b 作为采样范围的参数
    return {
        x: a*50,
        y: b*50
    };
    // 该表面是一个 50*50 单元区域
}
</script> 
```

然后，编写形状描绘代码：

```
<script>
var canvas = document.body.appendChild(document.createElement("canvas")),
    context = canvas.getContext("2d"),
    a, b, position;

for (a = 0; a < 1; a += .1) {
    for (b = 0; b < 1; b += .1) {
        position = surface(a, b);
        context.fillRect(position.x, position.y, 1, 1);
    }
}
</script> 
```

这时，看到的效果是这样的： ![enter image description here](img/1.jpg) 现在，尝试一下更密集的采样间隔： ![enter image description here](img/2.jpg) 正如现在所看到的，因为采样间隔越来越密集，点越来越接近，到最高密度时，相邻点之间的距离小于一个像素，肉眼就看不到间隔（见 0.01）。为了不造成太大的视觉差，再进一步缩小采样间隔，此时，绘制区已经填满（比较结果为 0.01 和 0.001）。

接下来，我用这个公式来绘制一个圆形：（X-X0）^ 2 +（Y-Y0）^ 2 <半径^ 2，其中（X0，Y0）为圆心：

```
<script>
function surface(a, b) {
    var x = a * 100,
        y = b * 100,
        radius = 50,
        x0 = 50,
        y0 = 50;

    if ((x - x0) * (x - x0) + (y - y0) * (y - y0) < radius * radius) {
        // 圆内
        return {
            x: x,
            y: y
        };
    } else {
        // 圆外
        return null;
    }
}
</script> 
```

为了防止溢出，还要加上一个采样条件：

```
<script>
if (position = surface(a, b)) {
    context.fillRect(position.x, position.y, 1, 1);
}
</script> 
```

结果如下： ![enter image description here](img/3.jpg) 有不同的方法来定义一个圆，其中一些并不需要拒绝采样。我并无一定要使用哪一种来定义圆圈的意思，所以下面用另一种方法来定义一个圆：

```
<script>
function surface(a, b) {
    // 使用极坐标，快回去翻微积分吧！
    var angle = a * Math.PI * 2,
        radius = 50,
        x0 = 50,
        y0 = 50;

    return {
        x: Math.cos(angle) * radius * b + x0,
        y: Math.sin(angle) * radius * b + y0
    };
}

</script> 
```

![enter image description here](img/3.jpg) （此方法相比前一个方法需要密集采样以进行填充。） 好了，现在让圆变形，以使它看起来更像是一个花瓣：

```
<script>
function surface(a, b) {
    var x = a * 100,
        y = b * 100,
        radius = 50,
        x0 = 50,
        y0 = 50;

    if ((x - x0) * (x - x0) + (y - y0) * (y - y0) < radius * radius) {
        return {
            x: x,
            y: y * (1 + b) / 2 // 变形
        };
    } else {
        return null;
    }
} 
```

![enter image description here](img/4.jpg) 这看起来已经很像一个玫瑰花瓣的形状了。在这里也可以试试通过修改一些函数数值，将会出现很多有趣的形状。

接下来应该给它添加色彩了：

```
<script>
function surface(a, b) {
    var x = a * 100,
        y = b * 100,
        radius = 50,
        x0 = 50,
        y0 = 50;

    if ((x - x0) * (x - x0) + (y - y0) * (y - y0) < radius * radius) {
        return {
            x: x,
            y: y * (1 + b) / 2,
            r: 100 + Math.floor((1 - b) * 155), // 添加梯度
            g: 50,
            b: 50
        };
    } else {
        return null;
    }
}

for (a = 0; a < 1; a += .01) {
    for (b = 0; b < 1; b += .001) {
        if (point = surface(a, b)) {
            context.fillStyle = "rgb(" + point.r + "," + point.g + "," + point.b + ")";
            context.fillRect(point.x, point.y, 1, 1);
        }
    }
} 
```

![enter image description here](img/5.jpg) 一片带色的花瓣就出现了。

## 3D 曲面和透视投影

定义三维表面很简单，比如，来定义一个管状物体：

```
<script>
function surface(a, b) {
    var angle = a * Math.PI * 2,
        radius = 100,
        length = 400;

    return {
        x: Math.cos(angle) * radius,
        y: Math.sin(angle) * radius,
        z: b * length - length / 2, // 减去一般的长度，使得焦点在三维坐标中心点(0,0,0)
        r: 0,
        g: Math.floor(b * 255),
        b: 0
    };
}
</script> 
```

接着添加投影透视图，首先需要我们定义一个摄像头： ![enter image description here](img/6.jpg) 如上图，将摄像头放置在（0，0，Z）位置，画布在 X / Y 平面。投影到画布上的采样点为：

```
<script>
var pX, pY,  // 画布 X 和 Y 轴的坐标
    perspective = 350,
    halfHeight = canvas.height / 2,
    halfWidth = canvas.width / 2,
    cameraZ = -700;

for (a = 0; a < 1; a += .001) {
    for (b = 0; b < 1; b += .01) {
        if (point = surface(a, b)) {
            pX = (point.x * perspective) / (point.z - cameraZ) + halfWidth;
            pY = (point.y * perspective) / (point.z - cameraZ) + halfHeight;
            context.fillStyle = "rgb(" + point.r + "," + point.g + "," + point.b + ")";
            context.fillRect(pX, pY, 1, 1);
        }
    }
}
</script> 
```

![enter image description here](img/7.jpg)

## z-buffer

z-buffer 在计算机图形学中是一个相当普遍的技术，在为物件进行着色时，执行“隐藏面消除”工作，使隐藏物件背后的部分就不会被显示出来。 ![enter image description here](img/8.jpg) 上图是用 z-buffer 技术处理后的玫瑰。(可以看到已经具有立体感了)

talk is cheap, show the code!

```
<script>
var zBuffer = [],
    zBufferIndex;

for (a = 0; a < 1; a += .001) {
    for (b = 0; b < 1; b += .01) {
        if (point = surface(a, b)) {
            pX = Math.floor((point.x * perspective) / (point.z - cameraZ) + halfWidth);
            pY = Math.floor((point.y * perspective) / (point.z - cameraZ) + halfHeight);
            zBufferIndex = pY * canvas.width + pX;
            if ((typeof zBuffer[zBufferIndex] === "undefined") || (point.z < zBuffer[zBufferIndex])) {
                zBuffer[zBufferIndex] = point.z;
                context.fillStyle = "rgb(" + point.r + "," + point.g + "," + point.b + ")";
                context.fillRect(pX, pY, 1, 1);
            }
        }
    }
}
</script> 
```

## 旋转

你可以使用任何矢量旋转的方法。在代码玫瑰的创建中，我使用的是欧拉旋转。现在将之前编写的管状物进行旋转，实现绕 Y 轴旋转：

```
<script>
    var angle = a * Math.PI * 2,
        radius = 100,
        length = 400,
        x = Math.cos(angle) * radius,
        y = Math.sin(angle) * radius,
        z = b * length - length / 2,
        yAxisRotationAngle = -.4, // 弧度
        rotatedX = x * Math.cos(yAxisRotationAngle) + z * Math.sin(yAxisRotationAngle),
        rotatedZ = x * -Math.sin(yAxisRotationAngle) + z * Math.cos(yAxisRotationAngle);

    return {
        x: rotatedX,
        y: y,
        z: rotatedZ,
        r: 0,
        g: Math.floor(b * 255),
        b: 0
    };
}
</script> 
```

![enter image description here](img/9.jpg)

## 蒙特卡罗方法

关于采样时间，间隔过大过小都会引起极差的视觉感受，所以，需要设置合理的采样间隔，这里使用蒙特卡罗方法。

```
<script>
var i;

window.setInterval(function () {
    for (i = 0; i < 10000; i++) {
        if (point = surface(Math.random(), Math.random())) {
            pX = Math.floor((point.x * perspective) / (point.z - cameraZ) + halfWidth);
            pY = Math.floor((point.y * perspective) / (point.z - cameraZ) + halfHeight);
            zBufferIndex = pY * canvas.width + pX;
            if ((typeof zBuffer[zBufferIndex] === "undefined") || (point.z < zBuffer[zBufferIndex])) {
                zBuffer[zBufferIndex] = point.z;
                context.fillStyle = "rgb(" + point.r + "," + point.g + "," + point.b + ")";
                context.fillRect(pX, pY, 1, 1);
            }
        }
    }
}, 0);
</script> 
```

设置 a 和 b 为随机参数，用足够的采样完成表面填充。我每次绘制 10000 点，然后静待屏幕完成更新。

另外需要注意的是，如果随机数发生错误时，表面填充效果会出错。有些浏览器中，Math.random 的执行是线性的，这就有可能导致表面填充效果出错。这时，就得使用类似 Mersenne Twister（一种随机数算法）这样的东西去进行高质量的 PRNG 采样，从而避免错误的发生。

## 完成源码（开发版）

```
<!DOCTYPE HTML>
<html>  
<head>  
<title>Rose</title>  
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">  
</head>  
<body style="margin-left:200px">  
<div style="text-align: center"> 
    <canvas id="c"></canvas> 
</div>

<script type="text/javascript">
    var canvas = document.getElementsByTagName('canvas')[0];
    var context = canvas.getContext('2d'); 

    var a = context; 
    var b = document.body;
    var c = canvas;

    document.body.clientWidth; 
    var zBuffer = []; 
    var SIZE = 777;
    canvas.width = canvas.height = SIZE;
    var h = -350; 

    function surface(a, b, c) {  
        if (c > 60) {  
            return {   
                x : Math.sin(a * 7) * (13 + 5 / (.2 + Math.pow(b * 4, 4)))  - Math.sin(b) * 50,  
                y : b * SIZE + 50,   
                z : 625 + Math.cos(a * 7)   * (13 + 5 / (.2 + Math.pow(b * 4, 4))) + b * 400,  
                r : a * 1 - b / 2,  g : a  };  
            } 

        var A = a * 2 - 1;
        var B = b * 2 - 1; 

        if (A * A + B * B < 1) {
        if (c > 37) { 
            var j = c & 1;  
            var n = j ? 6 : 4;   
            var o = .5 / (a + .01) + Math.cos(b * 125) * 3 - a * 300;  
            var w = b * h;  
            return {   
                x : o * Math.cos(n) + w * Math.sin(n) + j * 610 - 390,  
                y : o * Math.sin(n) - w * Math.cos(n) + 550 - j * 350,  
                z : 1180 + Math.cos(B + A) * 99 - j * 300,  
                r : .4  - a  * .1   + Math.pow(1 - B * B, -h * 6)  * .15  - a  * b  * .4   + Math.cos(a + b)  / 5   + Math.pow(Math.cos((o * (a + 1) + (B > 0 ? w  : -w)) / 25), 30) * .1 * (1 - B * B),  g : o / 1e3 + .7 - o * w * 3e-6
            };  
        }

        if (c > 32) {  
            c = c * 1.16 - .15;  
            var o = a * 45 - 20;  
            var w = b * b * h;  
            var z = o * Math.sin(c) + w * Math.cos(c) + 620;  
            return {  
                x : o * Math.cos(c) - w * Math.sin(c),  
                y : 28 + Math.cos(B * .5) * 99 - b * b * b * 60 - z / 2  - h,  
                z : z,  
                r : (b * b * .3 + Math.pow((1 - (A * A)), 7) * .15 + .3)  * b,  
                g : b * .7  
            };  
        }

        var o = A * (2 - b) * (80 - c * 2);  
        var w = 99 - Math.cos(A) * 120 - Math.cos(b) * (-h - c * 4.9)  + Math.cos(Math.pow(1 - b, 7)) * 50 + c * 2;  
        var z = o * Math.sin(c) + w * Math.cos(c) + 700;  
        return {  
            x : o * Math.cos(c) - w * Math.sin(c),  
            y : B * 99 - Math.cos(Math.pow(b, 7)) * 50 - c / 3 - z  / 1.35 + 450,  z : z,  
            r : (1 - b / 1.2) * .9 + a * .1,  
            g : Math.pow((1 - b), 20) / 4 + .05  
        };  
        }  
    }   

    setInterval(function() {  
        for ( var i = 0; i < 10000; i++) {   
            var part = i % 46;   
            var c = part / .74;   
            var point = surface(Math.random(), Math.random(), c);  
            if (point) {  
                var z = point.z;  
                var x = parseInt(point.x * SIZE / z - h);  
                var y = parseInt(point.y * SIZE / z - h);  
                var zBufferIndex = y * SIZE + x;  
                if ((typeof zBuffer[zBufferIndex] === "undefined")  || (zBuffer[zBufferIndex] > z)) {  
                    zBuffer[zBufferIndex] = z;   
                    var r = -parseInt(point.r * h);  var g = -parseInt(point.g * h);  
                    var b = -parseInt(point.r * point.r * -80);   
                    context.fillStyle = "rgb(" + r + "," + g + "," + b  + ")";  
                    context.fillRect(x, y, 1, 1);  
                }  
            }  
        }  
    }, 0);  

</script>

</body>  
</html> 
```

![enter image description here](img/10.jpg) 为了使玫瑰的每个部分在同一时间完成并呈现，还需要添加一个功能，为每部分设置一个参数以返回值来进行同步。并用一个分段函数代表玫瑰的各个部分。比如在花瓣部分，可以使用旋转和变形来创建它们。

虽然表面采样方法是创建三维图形非常著名的、最古老的方法之一，但这种把蒙特卡罗、z-buffer 加入到表面采样中的方法并不常见。对于现实生活场景的制作，这也许算不上很有创意，但它简易的代码实现和很小的体积仍令人满意。

## Happy Valentine's Day