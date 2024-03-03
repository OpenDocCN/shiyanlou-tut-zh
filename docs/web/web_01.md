# SCSS（SASS）画小黄人

SCSS（SASS）画小黄人，学习 CSS3。

## 一、项目简介

通过 SCSS（SASS）画一个会眨眼睛的小黄人，主要学习 CSS3。

## 二、搭建环境

安装 SASS 需要 Ruby 支持，环境中已经安装了 Ruby，直接安装 SASS：

```
$ sudo gem install sass 
```

创建项目：

```
$ mkdir minion
$ cd minion
$ touch minion.html
$ mkdir sass
$ cd sass
$ touch minion.scss 
```

上面，我们创建了 minion 文件夹，然后在 minion 文件夹中创建了 sass 文件夹和`minion.html`文件。sass 文件夹用于放 sass 文件。

打开`minion.html`文件，输入如下代码：

```
<!doctype html>
<html>
<head>
  <title>minion</title>
  <link rel="stylesheet" href="sass/minion.css">
</head>

<body>
</body>
</html> 
```

## 三、绘制小黄人

写代码之前，我们先看看效果图吧：

![minion](img/minion.jpg)

首先打开 HTML 文件，在`body`中添加如下代码：

```
<body>
    <section class="content">
    </section>
</body> 
```

我们添加了一个`section.content`，我们就把小黄人放在这个`section`里面。

我们可以看到，小黄人有 8 根头发，我们在`section`中添加两个列表，分别表示小黄人左边的四根头发和右边的四根头发，再加一个 div 表示身体：

```
<body>
    <section class="content">
        <ul class="hair hair-left">
            <li></li>
            <li></li>
            <li></li>
            <li></li>
        </ul>
        <ul class="hair hair-right">
            <li></li>
            <li></li>
            <li></li>
            <li></li>
        </ul>
        <div class="body">
        </div>
    </section>
</body> 
```

然后在 sass 文件夹中创建`_body.scss`文件，输入如下代码：

```
body {
    overflow: hidden;
}
.content {
    margin: 40px;
}

.body {
    position: relative;
    width: 180px;
    height: 325px;
    margin: 0 auto;
    border-radius: 85px 85px 0 0;
    box-shadow: inset 0 -10px 10px 3px #cc9e24; // 设置内部阴影
    background: #fcda6d;
    background: -webkit-gradient(linear, right top, left top,color-stop(61%, #fcda6d), color-stop(100%, #cc9e24)); 
    background: -webkit-linear-gradient(right, #fcda6d 67%,#cc9e24 100%);
    background:    -moz-linear-gradient(right, #fcda6d 67%,#cc9e24 100%);
    background:     -ms-linear-gradient(right, #fcda6d 67%,#cc9e24 100%);
    background:      -o-linear-gradient(right, #fcda6d 67%,#cc9e24 100%); 
    background:         linear-gradient(to right, #fcda6d 67%,#cc9e24 100%);
} 
```

然后在`minion.scss`文件中引入`_body.scss`：

```
@import 'body'; 
```

编译 SASS，打开终端，进入 minion 目录输入如下命令：

```
$ sass minion.scss minion.css 
```

每次修改了 SCSS 代码，都需要重新编译一下，在后面我们创建的所有`_xxx.scss`这样的文件都需要在`minion.scss`文件中用`import`引入，而且文件都放在 sass 文件夹中。然后用浏览器打开`minion.html`文件就能看到效果了。

我们给小黄人的身体定义了宽度和高度，并且使其居中，然后设置了头部的 85px 圆角，这样小黄人的头就是圆的了。最后设置了内部阴影背景颜色，这样让小黄人的身体边缘看起来有颜色渐变的效果和立体感。

`background`中用到了`linear-gradient()`方法，`linear gradient`是线性渐变的意思，这个方法可以创建一个线性渐变的背景颜色。

`linear-gradient([[ <angle> | to <side-ro-corner> ],]? <color-stop>[, <color-stop>]+)`

第一个可选参数可以是一个角度或者边角，如果是角度的话，是从底部顺时针开始旋转的。如果是边角的话则是指定上下左右边或者左上角、右上角、左下角和右下角，比如`to top`就是从下到上渐变，`to left top`就是从右下角到左上角渐变，`to top left`和`to left top`是一样的。

第二个参数和第三个参数就是颜色了，表示从第二个参数颜色渐变到第三个参数的颜色，当然也可以传递两个以上的颜色，比如传递三个颜色，那么就需要指定什么时候渐变到第二个颜色，比如

```
/* 这两条效果一样 */
/* 所以 0 度是从底部开始的 */
background:  linear-gradient(to top, blue, green 40%, red);
background:  linear-gradient(0deg, blue, green 40%, red); 
```

百分数表示从背景整体的百分比开始，上面表示从背景 40%的地方开始出现绿色。

下面我们来处理头发，创建`_hair.scss`文件：

```
.hair {
    padding: 0;

    li {
        position: absolute;
        top: -1px;
        left: 50%;
        width: 5px;
        height: 45px;
        margin: 10px;
        list-style: none;
        border: solid 2px #555;
        border-radius: 140%;
        z-index: 2;
    }
} 
```

把头发定位到小黄人头部上方，然后设置圆角和边框使头发看起来是弯曲的，设置`z-index`使头发显示在头部前面。现在所有头发在一起，是一个椭圆，下面我们来把头发一根根分开：

```
// 左边的头发
.hair-left {
    li {
        border-left: none;
        border-bottom: none;

        // 第一根头发
        &:nth-child(1) {
            margin: 20px 0 0 -60px;    // 控制头发位置
            transform: rotate(-20deg); // 控制头发方向
        }
        // 第二根头发
        &:nth-child(2) {
            height: 30px;
            margin: 45px 0 0 -75px;
            transform: rotate(-50deg);
        }
        // 第三根头发
        &:nth-child(3) {
            height: 36px;
            margin: 15px 0 0 -35px;
            transform: rotate(-10deg);
        }
        // 第四根头发
        &:nth-child(4) {
            height: 26px;
            margin: 35px 0 0 -13px;
            transform: rotate(-20deg);
        }
    }
} 
```

根据情况设置头发的不同长度，然后通过边距设置头发的位置，在通过旋转设置头发的方向，`tranform`属性可以向元素应用 2D 或者 3D 转换，`rotate`则是定义 2D 旋转。同样，设置右边的四根头发：

```
// 右边的头发
.hair-right {
    li {
        border-right: none;
        border-bottom: none;

        // 第一根头发
        &:nth-child(1) {
            margin: 23px 0 0 60px;
            transform: rotate(20deg);
        }
        // 第二根头发
        &:nth-child(2) {
            height: 30px;
            margin: 45px 0 0 75px;
            transform: rotate(50deg);
        }
        // 第三根头发
        &:nth-child(3) {
            height: 36px;
            margin: 15px 0 0 35px;
            transform: rotate(10deg);
        }
        // 第四根头发
        &:nth-child(4) {
            height: 34px;
            margin: 20px 0 0 13px;
            transform: rotate(20deg);
        }
    }
} 
```

头发展出出来了，现在我们来画小黄人的眼镜，在`div.body`中添加 HTML：

```
 <div class="body">
        <div class="glasses">
            <span class="band band-left"></span>
            <span class="band band-right"></span>
            <div class="glass">
                <div class="iris iris-left">
                </div>  
            </div>
            <div class="glass">
                <div class="iris iris-right">
                </div>
            </div>
        </div>
    </div> 
```

`.band-left`和`.band-right`分别是眼镜左边和右边的眼镜带子，两个`.glass`分别是左右两个镜框，`iris`是眼球。下面我们来画眼镜带子，创建`_glasses.scss`文件：

```
.glasses {
    width: 180px;
}

.band,
.band-left:before,
.band-right:before {
    position: relative;
    display: block;
    width: 17px;
    height: 10px;
    background: #222;
    border-radius: 3px;
    box-shadow: 0 1px 5px 0px #222;
}
// 左边的带子
.band-left {
    top: 96px;
    left: -5px;
    transform: rotate(-5deg);

    &:before {
        content: "";
        display: block;
        top: 10px;
        background: #333;
    }
}
// 右边的带子
.band-right {
    top: 89px;
    left: 168px;
    transform: rotate(5deg);

    &:before {
        content: "";
        display: block;
        top: 10px;
        background: #333;
    }
} 
```

通过相对定位控制带子的位置，然后旋转一点角度，通过`:before`属性复制带子，这样就不用多定义一个 div 了。

下面来画眼镜：

```
.glass {
    float: left;
    width: 85px;
    height: 85px;
    margin: 40px 0 0 10px;
    border-radius: 110px;
    background: linear-gradient(#989697, #696371);
    box-shadow: inset 0 -2px 2px #5d4b3d,
                inset -1px 1px 3px 1px #fff,
                1px 5px 7px -1px #c48e00;
} 
```

`box-shadow`设置了多个阴影，两个内部阴影和一个外部阴影。

通过`:before`属性去掉镜框中间部分，通过`:after`属性添加眼睛白色部分：

```
.glass {
    float: left;
    width: 85px;
    height: 85px;
    margin: 40px 0 0 10px;
    border-radius: 110px;
    background: linear-gradient(#989697, #696371);
    box-shadow: inset 0 -2px 2px #5d4b3d,
            inset -1px 1px 3px 1px #fff,
            1px 5px 7px -1px #c48e00;

    &:before {
        content: "";
        display: block;
        width: 65px;
        height: 65px;
        position: relative;
        top: 10px;
        left: 10px;
        background: #fcda6d;
        border-radius: 70px;
        box-shadow: inset 0 2px 4px 1px #5d4b3d;
    }
    &:after {
        content: "";
        display: block;
        width: 63px;
        height: 50px;
        position: relative;
        top: -73px;
        left: 11px;
        border-radius: 70px;
        background: #FFF; /* Old browsers */
        background: -webkit-gradient(linear,left bottom,right top,color-stop(0.54, #FFF), color-stop(0.91, #AAA));
        background: -webkit-linear-gradient(left bottom,right top, #FFF 54%,#AAA 91%);
        background:    -moz-linear-gradient(left bottom,right top, #FFF 54%,#AAA 91%);
        background:     -ms-linear-gradient(left bottom,right top, #FFF 54%,#AAA 91%);
        background:      -o-linear-gradient(left bottom,right top, #FFF 54%,#AAA 91%);
        background:         linear-gradient(to left bottom, #FFF 53%,#AAA 91%);
    }
    &:last-child {
        margin-left: -9px;
    }
} 
```

然后画眼球：

```
.iris {
    width: 23px;
    height: 23px;
    position: relative;
    top: -30px;
    border: 1px solid #222;
    border-radius: 50%;
    background: #000;
    box-shadow: inset -2px -2px 5px 2px #222,
              inset 2px 2px 1px 2px #7e4d49;
    background: -webkit-radial-gradient(center, ellipse cover, #000 25%, #6f4a2d 34%, #c79067 44%, #6f4a2d 50%);
    background: -moz-radial-gradient(center, ellipse cover, #000 25%, #6f4a2d 34%, #c79067 44%, #6f4a2d 50%);
    z-index: 10;

    // 绘制眼球反光
    &:before {
        content: "";
        display: block;
        width: 5px;
        height: 5px;
        position: relative;
        top: 4px;
        left: 4px;
        background: #FFF;
        border-radius: 50%;
        box-shadow: 0 0 5px 2px #FFF;
        z-index: 11;
    }
}
.iris-left {
    left: 38px; // 调整眼球距离
}

.iris-right {
    left: 23px; // 调整眼球距离
} 
```

通过`radial-gradient()`绘制眼球虹膜，`radial-gradient()`用于绘制径向渐变效果。

用法和`linear-gradient()`类似，第一个参数表示圆的直径和圆心位置(不设置的话，默认为当前元素的背景大小，圆心即为背景中心)或者直接设置形状（比如 circle 等），后面的参数表示渐变的颜色，如：

```
/* 渐变的圆心坐标为（100px, 100px），圆的直径为 100px */
/* 颜色渐变从圆心到圆边缘开始，红色、绿色、蓝色，所以圆心为红色，圆边缘为蓝色 */
background: radial-gradient(100px at 100px 100px, red, green, blue);

/* 同样可以设置颜色渐变范围 */
/* 从圆心算起，10%的地方开始变为绿色 */
background: radial-gradient(100px at 100px 100px, red 10%, green, blue); 
```

在`section.content`中添加嘴巴的 HTML 代码：

```
 <div class="mouth">
        <ul class="teeth">
            <li></li>
            <li></li>
            <li></li>
            <li></li>
        </ul>
    </div> 
```

其中的列表表示牙齿，然后添加嘴的 SCSS 代码，创建`_mouth.scss`文件：

```
.mouth {
    width: 70px;
    height: 30px;
    margin: 0 auto;
    position: relative;
    top: -155px;
    border-bottom-left-radius: 50px;  // 使嘴巴下方呈圆角
    border-bottom-right-radius: 50px; // 使嘴巴下方呈圆角
    border: 0;
    background: #222;
    background: -webkit-gradient(linear, 0 0, 0 100%, from(#222), color-stop(0.79, #bd736a));
    background: -webkit-linear-gradient(#222, #bd736a 99%);
    background:    -moz-linear-gradient(#222, #bd736a 99%);
    background:      -o-linear-gradient(#222, #bd736a 99%);
    background:         linear-gradient(#222, #bd736a 99%);
    overflow: hidden;
    z-index: 2;

    &:after {
        content: "";
        display: block;
        position: relative;
        top: -50px;
        left: -21px;
        width: 120px;
        height: 40px;
        border-radius: 50%;
        background: #FCDA6D;
        box-shadow: inset 0 0 3px 1px #957b43;
    }
} 
```

添加牙齿的 SCSS 代码：

```
.teeth {
    width: 90px;
    padding: 0 5px;
    position: relative;
    top: -19px;

    li {
        display: block;
        float: left;
        width: 16px;
        height: 15px;
        list-style: none;
        z-index: 1;
        border-radius: 6px;
        background: #ccccc2;
        box-shadow: inset 0 -1px 1px 1px #FFF,
                    inset -1px 0 1px 0px #F45;

        // 第一颗和最后一颗牙齿改短一点
        &:first-child {
            height: 12px;
        }
        &:last-child {
            height: 12px;
        }
    }
} 
```

在`section.content`中添加裤子的 HTML：

```
 <div class="pants">
            <div class="belt belt-left"></div>
            <div class="belt belt-right"></div>
        </div> 
```

然后添加 SCSS 代码，创建`_pants.scss`文件：

```
.pants {
    width: 180px;
    height: 50px;
    margin: 0 auto;
    position: relative;
    z-index: 2;
    top: -58px;
    border-radius: 2px 2px 25px 25px;
    background: #146696;
    background: -webkit-linear-gradient(left, #146696 67%,#115278 100%);
    border: 2px dotted #1f4362;
    box-shadow: inset 1px -10px 10px 2px #1a364d,
              0 0 2px 2px #2e5f88;

    &:before {
        width: 120px;
        height: 60px;
        content: "";
        display: block;
        position: relative;
        top: -50px;
        left: 40px;
        border: 2px dotted #1f4362;
        border-bottom: 0;
        border-radius: 10px;
        background: #146696;
        background-image: -webkit-gradient(linear, left, color-stop(67%, #146696), color-stop(100%, #115278));
        background-image: -webkit-linear-gradient(left, #146696 67%, #115278 100%);
        background-image:    -moz-linear-gradient(left, #146696 67%, #115278 100%);
        background-image:     -ms-linear-gradient(left, #146696 67%, #115278 100%);
        background-image:      -o-linear-gradient(left, #146696 67%, #115278 100%);
        background-image:         linear-gradient(left, #146696 67%, #115278 100%);
        box-shadow: 0 -3px 2px 2px #2e5f88;
    }
}

.belt {
    position: relative;
    width: 15px;
    height: 75px;
    background: #146696;
    box-shadow: inset 1px 10px 10px 2px #1a364d;

    &:after {
        content: "";
        display: block;
        width: 10px;
        height: 10px;
        border-radius: 50%;
        background: #223333;
        position: absolute;
        bottom: 5px;
        left: 2px;
    }
}
.belt-left {
    top: -160px;
    left: 18px;
    border: 2px dotted #1f4362;
    border-top-left-radius: 25px;
    -webkit-transform: rotate(-55deg);
     -moz-transform: rotate(-55deg);
      -ms-transform: rotate(-55deg);
       -o-transform: rotate(-55deg);
          transform: rotate(-55deg);
}

.belt-right {
    height: 60px;
    top: -230px;
    left: 158px;
    border: 2px dotted #1F4362;
    border-top-right-radius: 25px;
    -webkit-transform: rotate(35deg);
     -moz-transform: rotate(35deg);
      -ms-transform: rotate(35deg);
       -o-transform: rotate(35deg);
          transform: rotate(35deg);
} 
```

在`section.content`中添加手臂的 HTML：

```
 <div class="arm arm-left">
        <div class="hand">
            <ul class="fingers fingers-left">
                <li class="finger"></li>
                <li class="finger"></li>
                <li class="finger"></li>
            </ul>
        </div>
    </div>
    <div class="arm arm-right">
        <div class="hand">
            <ul class="fingers fingers-right">
                <li class="finger"></li>
                <li class="finger"></li>
            </ul>
        </div>
    </div> 
```

添加肩膀的 SCSS，创建`_arm.scss`文件：

```
.arm {
    width: 20px;
    height: 100px;
    margin: 0 auto;
    position: relative;
    z-index: 2;
    border-radius: 10px;
    background: #FFD449;
}
.arm-right {
    height: 115px;
    top: -276px;
    left: 95px;
    box-shadow: inset 0 10px 10px 3px #D5970E;
    z-index: 1;
    -webkit-transform: rotate(-10deg);
     -moz-transform: rotate(-10deg);
      -ms-transform: rotate(-10deg);
       -o-transform: rotate(-10deg);
          transform: rotate(-10deg);
}
.arm-left {
    top: -159px;
    left: -104px;
    -webkit-transform: rotate(10deg);
     -moz-transform: rotate(10deg);
      -ms-transform: rotate(10deg);
       -o-transform: rotate(10deg);
          transform: rotate(10deg);

    &:before {
        content: "";
        display: block;
        width: 21px;
        height: 40px;
        position: relative;
        top: -18px;
        border-radius: 50%;
        background: #FFD449;
        -webkit-transform: rotate(10deg);
         -moz-transform: rotate(10deg);
          -ms-transform: rotate(10deg);
           -o-transform: rotate(10deg);
              transform: rotate(10deg);
    }
}
.arm {
    &:after {
        content: "";
        display: block;
        width: 22px;
        height: 20px;
        position: relative;
        border-radius: 6px;
        background: #FFD449;
    }
    &.arm-left {
        &:after {
            z-index: 3;
            top: -14px;
            -webkit-transform: rotate(-15deg);
             -moz-transform: rotate(-15deg);
              -ms-transform: rotate(-15deg);
               -o-transform: rotate(-15deg);
                  transform: rotate(-15deg);
        }
    }
    &.arm-right {
        &:after {
            z-index: 3;
            top: 55px;
            left: -2px;
            box-shadow: inset -3px -6px 5px 1px #D5970E;
            -webkit-transform: rotate(15deg);
             -moz-transform: rotate(15deg);
              -ms-transform: rotate(15deg);
               -o-transform: rotate(15deg);
                  transform: rotate(15deg);
        }
    }
} 
```

添加手臂的 SCSS：

```
.hand {
    height: 40px;
    width: 35px;
    position: relative;
    z-index: 1;
    top: 35px;
    left: 0;
    border-radius: 30%;
    box-shadow: inset 0 -2px 10px 5px #222;
    background: #333;
    -webkit-transform: rotate(-20deg);
     -moz-transform: rotate(-20deg);
      -ms-transform: rotate(-20deg);
       -o-transform: rotate(-20deg);
          transform: rotate(-20deg);

    &:before {
        content: "";
        display: block;
        position: relative;
        z-index: 1;
        top: -5px;
        left:-3px;
        width: 30px;
        height: 9px;
        background: #111;
        border: 5px solid #222;
        border-radius: 50%;
        box-shadow: 0 4px 1px 0 #444;
    }
    &:after {
        content: "";
        display: block;
        position: relative;
        z-index: 1;
        top: -100px;
        left: 1px;
        width: 34px;
        height: 30px;
        background: #333;
        border-radius: 50%;
        box-shadow: inset 0 -10px 10px 5px #222;
        -webkit-transform: rotate(5deg);
         -moz-transform: rotate(5deg);
          -ms-transform: rotate(5deg);
           -o-transform: rotate(5deg);
              transform: rotate(5deg);
    }
}
.arm-right .hand {
    top: 105px;
    left: -15px;
    transform: rotate(20deg);
    -webkit-transform: rotate(20deg);
     -moz-transform: rotate(20deg);
      -ms-transform: rotate(20deg);
       -o-transform: rotate(20deg);
          transform: rotate(20deg);
}
.arm-left .hand:after {
    height: 30px;
    width: 30px;
    left: 3px;
    top: -110px;
} 
```

添加手指的 SCSS：

```
.fingers {
    list-style: none;
    position: relative;
    top: 10px;

    li {
        border-radius: 10px;
        position: relative;
        background: #333;
        box-shadow: inset 0 -10px 10px 5px #222;
    }
}
.fingers-right {
    li {
        &:nth-child(1) {
            z-index: 2;
            width: 20px;
            height: 35px;
            top: -20px;
            left: -50px;
            border-right: 2px solid #000;
            -webkit-transform: rotate(50deg);
             -moz-transform: rotate(50deg);
              -ms-transform: rotate(50deg);
               -o-transform: rotate(50deg);
                  transform: rotate(50deg);
        }
        &:nth-child(2) {
            z-index: 1;
            width: 20px;
            height: 30px;
            top: -50px;
            left: -40px;
            border-right: 2px solid #000;
            -webkit-transform: rotate(40deg);
             -moz-transform: rotate(40deg);
              -ms-transform: rotate(40deg);
               -o-transform: rotate(40deg);
                  transform: rotate(40deg);
        }
    }
}
.fingers-left {
    li {
        &:nth-child(1) {
            z-index: 2;
            width: 25px;
            height: 25px;
            top: -17px;
            left: -43px;
            border-right: 2px solid #000;
            border-radius: 30px;
            -webkit-transform: rotate(10deg);
             -moz-transform: rotate(10deg);
              -ms-transform: rotate(10deg);
               -o-transform: rotate(10deg);
                  transform: rotate(10deg);
        }
        &:nth-child(2) {
            z-index: 1;
            width: 20px;
            height: 24px;
            top: -50px;
            left: -18px;
            border-right: 2px solid #000;
            -webkit-transform: rotate(-30deg);
             -moz-transform: rotate(-30deg);
              -ms-transform: rotate(-30deg);
               -o-transform: rotate(-30deg);
                  transform: rotate(-30deg);
        }
        &:nth-child(3) {
            z-index: 1;
            width: 23px;
            height: 30px;
            top: -63px;
            left: -33px;
            border-right: 2px solid #000;
            -webkit-transform: rotate(0deg);
             -moz-transform: rotate(0deg);
              -ms-transform: rotate(0deg);
               -o-transform: rotate(0deg);
                  transform: rotate(0deg);
        }
    }
} 
```

添加脚的 HTML：

```
 <div class="legs">
            <div class="leg"></div>
            <div class="leg"></div>
        </div> 
```

添加脚的 SCSS，创建`_legs.scss`文件：

```
.legs {
    width: 120px;
    margin: 0 auto;

    .leg {
        width: 40px;
        height: 35px;
        display: inline-block;
        position: relative;
        top: -276px;
        left: 20px;
        background: #146696;
        border-radius: 30%;
        box-shadow: inset 1px 10px 10px 2px #222;
        z-index: 1;
    }
} 
```

添加鞋子 HTML：

```
 <div class="shoes shoes-left"></div>
        <div class="shoes shoes-right"></div> 
```

添加鞋子的 SCSS，创建`_shoes.scss`文件：

```
.shoes {
    position: relative;
    margin: 0 auto;
    background: #222;
    box-shadow: inset -2px 1px 10px 1px #666;
    z-index: 1;
}
.shoes-left {
    width: 40px;
    height: 30px;
    top: -293px;
    left: -20px;
    border-radius: 20px;
    box-shadow: inset 0 -3px 3px 1px #999;
    z-index: 0;

    &:after {
        content: "";
        display: block;
        width: 40px;
        height: 20px;
        position: relative;
        top: 5px;
        border-bottom: 7px solid #222;
        border-radius: 20px;
    }
}
.shoes-right {
    width: 50px;
    height: 20px;
    top: -320px;
    left: 30px;
    border-radius: 20px;
    border-right: 1px solid #000;
    box-shadow: inset -1px 1px 5px 1px #999;
    z-index: 0;
    -webkit-transform: rotate(10deg);
     -moz-transform: rotate(10deg);
      -ms-transform: rotate(10deg);
       -o-transform: rotate(10deg);
          transform: rotate(10deg);

    &:after {
        content: "";
        display: block;
        width: 50px;
        height: 20px;
        position: relative;
        top: -3px;
        border-bottom: 7px solid #111;
        border-radius: 20px;
    }
} 
```

到这里小黄人就画完了，项目中主要用到的 CSS3 属性有`linear-gradient`、`radial-gradient`、`transform`、`box-shadow`、`border-radius`等，编写 CSS 注重的是细节，需要多调试，`1px`也不能放过。