# HTML5 的 Canvas 小游戏

使用 HTML5 的 Canvas 新特性制作一个简单好玩的小游戏

## 用 HTML5 开发一个小游戏

## 课程简介

本实验将带领大家利用 HTML5 的 Canvas 特性，结合 js 来开发一个抓小怪物的小游戏。

## 先来一个游戏预览：

![此处输入图片的描述](img/document-uid7682labid1158timestamp1436864778423.jpg)

## 开始之前的准备

新建一个 games 文件夹，在里面创建一下三个东西：

1.  `game.html`
2.  `js/` 里面创建`game.js`
3.  `images/` 里面放三张图片，一张背景图片(background.jpg)，一张英雄图片(hero.jpg)，一张怪物的图片(monster.jpg)

打开命令行终端，来到桌面：使用`wget`获取小游戏所需要的图片：

```
cd Desktop

wget http://labfile.oss.aliyuncs.com/courses/361/games.zip 
```

解压，在命令行执行：

```
unzip games.zip 
```

得到`games/`文件夹之后，直接拖到 Brackets 编辑器进行开发就可以。我们后续的代码都是写在`game.js`中。

在`game.html`里面有以下几行简单的 HTML 代码：

```
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="utf-8">
        <title>Simple Canvas Game</title>
    </head>
    <body>
        <script src="js/game.js"></script>
    </body>
</html> 
```

我们在`game.html`引入了`game.js`文件，没错，剩下的所有工作都是在操作`game.js`，为其添加游戏的 js 代码。

## 创建画布

在 game.js 里面，我们首先需要为游戏的舞台创建一张画布(canvas):

```
var canvas = document.createElement("canvas");
var ctx = canvas.getContext("2d");
canvas.width = 512;
canvas.height = 480;
document.body.appendChild(canvas); 
```

这里通过 js 来创建了一个<canvas>元素并设置`canvas`的宽和高，最后将其添加到<body>标签后。`var ctx = canvas.getContext("2d");`中的`ctx`变量是我们后面会用到的，具体的 canvas 用法查看这里的链接：

https://developer.mozilla.org/en/canvas_tutorial

## 准备图片

游戏需要加载我们之前存放在 images 文件夹下面的三张图片：

```
// Background image
var bgReady = false;
var bgImage = new Image();
bgImage.onload = function () {
    bgReady = true;
};
bgImage.src = "images/background.jpg";

// Hero image
var heroReady = false;
var heroImage = new Image();
heroImage.onload = function () {
    heroReady = true;
};
heroImage.src = "images/hero.jpg";

// Monster image
var monsterReady = false;
var monsterImage = new Image();
monsterImage.onload = function () {
    monsterReady = true;
};
monsterImage.src = "images/monster.jpg"; 
```

以上三张图片都是通过创建简单的图片对象来实现加载的，类似 bgReady 的三个变量用来标识图片是否已经加载完成，如果在图片加载未完成情况下进行绘制是会报错的。如果你不太确定`new Image()`到底是个什么东西，你可以在`bgImage.src = "images/background.jpg";`之后使用`console.log(bgImage);`来查看，你看到的将是类似：

```
<img src="images/background.jpg" > 
```

## 游戏对象

我们需要定义一些对象，以便我们在后面会用到：

```
var hero = {
    speed: 256 // movement in pixels per second
};
var monster = {};
var monstersCaught = 0; 
```

既然是英雄抓获怪物，我们得要有一个`英雄`和`怪物`的对象。而`英雄`有一个`speed`属性用来控制他每秒移动多少像素。怪物游戏过程中不会移动，所以暂时不用设置属性。`monstersCaught`则用来存储怪物被捉住的次数，初始值当然为 0 了。

## 处理用户的输入

游戏是给人玩的，那么我们怎么知道用户到底在这个过程中干了什么？按了键盘？点了鼠标？这些都是用户在玩游戏的时候的输入，所以我们一旦捕获到这些输入，我们就可以根据游戏的逻辑对用户的输入进行处理了：

```
// Handle keyboard controls
var keysDown = {};

addEventListener("keydown", function (e) {
    keysDown[e.keyCode] = true;
}, false);

addEventListener("keyup", function (e) {
    delete keysDown[e.keyCode];
}, false); 
```

这里我们只是监听两个用户的输入：

1.  keydown
2.  keyup

然后我们将用户的输入先保存起来，并没有立即响应。为此，我们用`keysDown`这个对象来保存用户按下的键值`(keyCode)`，如果按下的键值在这个对象里，那么我们就做相应处理。

>在前端开发中，一般是用户触发了点击事件然后才去执行动画或发起异步请求之类的

## 开始一轮游戏

游戏在结束的时候，我们需要开始新的一轮游戏，所以在`game.js`添加`reset`函数

```
// Reset the game when the player catches a monster
var reset = function () {
    hero.x = canvas.width / 2;
    hero.y = canvas.height / 2;

    // Throw the monster somewhere on the screen randomly
    monster.x = 32 + (Math.random() * (canvas.width - 64));
    monster.y = 32 + (Math.random() * (canvas.height - 64));

}; 
```

`reset()`函数用于开始新一轮和游戏，在这个方法里我们将英雄放回画布中心同时将怪物放到一个随机的地方。

## 更新对象

在游戏的过程中，不管是用户在玩（有正确输入的状态）还是游戏结束，我们都是需要及时更新游戏的对象：

```
var update = function (modifier) {
    if (38 in keysDown) { // Player holding up
        hero.y -= hero.speed * modifier;
    }
    if (40 in keysDown) { // Player holding down
        hero.y += hero.speed * modifier;
    }
    if (37 in keysDown) { // Player holding left
        hero.x -= hero.speed * modifier;
    }
    if (39 in keysDown) { // Player holding right
        hero.x += hero.speed * modifier;
    }

    // Are they touching?
    if (
        hero.x <= (monster.x + 32)
        && monster.x <= (hero.x + 32)
        && hero.y <= (monster.y + 32)
        && monster.y <= (hero.y + 32)
    ) {

        ++monstersCaught;
        reset();
    }

}; 
```

`update`函数负责更新游戏的各个对象，会被规律地重复调用。首先它负责检查用户当前按住的是中方向键，然后将英雄往相应方向移动。

有点费脑力的或许是这个传入的`modifier` 变量。你可以在`后面`将要实现的`main` 方法里看到它的来源，但这里还是有必要详细解释一下。它是基于 1 开始并随时间变化的一个因子。例如 1 秒过去了，它的值就是 1，英雄的速度将会乘以 1，也就是每秒移动 256 像素；如果半秒钟则它的值为 0.5，英雄的速度就乘以 0.5 也就是说这半秒内英雄以正常速度一半的速度移动。理论上说因为这个`update`函数被调用的非常快且频繁，所以`modifier`的值会很小，但有了这一因子后，不管我们的代码跑得快慢，都能够保证英雄的移动速度是恒定的。

这里需要说明一下下面的判断怪物和英雄是什么根据：

```
 if (
        hero.x <= (monster.x + 31)
        && monster.x <= (hero.x + 31)
        && hero.y <= (monster.y + 32)
        && monster.y <= (hero.y + 32)
    ) 
```

上面的`31`,`32`是由`hero`和`monster`图片的大小决定的，我们的 hero 图片是`32x32`，monster 图片是`30x32`，所以根据坐标的位于图片中心的法制，就可以得到上面的判断条件。

现在英雄的移动已经是基于用户的输入(按下`上`，`下`，`左`，`右`键)了，接下来该检查移动过程中所触发的事件了，也就是英雄与怪物相遇。这就是本游戏的胜利点，`monstersCaught +1`然后重新开始新一轮。

## 渲染物体

之前写的代码都是在准备前期工作和处理一些游戏的状态等，下面将进入正题：我们需要将所有的东西画出来

```
// Draw everything
var render = function () {
    if (bgReady) {
        ctx.drawImage(bgImage, 0, 0);
    }

    if (heroReady) {
        ctx.drawImage(heroImage, hero.x, hero.y);
    }

    if (monsterReady) {
        ctx.drawImage(monsterImage, monster.x, monster.y);
    }

    // Score
    ctx.fillStyle = "rgb(250, 250, 250)";
    ctx.font = "24px Helvetica";
    ctx.textAlign = "left";
    ctx.textBaseline = "top";
    ctx.fillText("Goblins caught: " + monstersCaught, 32, 32);
}; 
```

这里的`ctx`就是最前面我们创建的变量。然后利用`canvas`的`drawImage()`首先当然是把背景图画出来。然后如法炮制将英雄和怪物也画出来。这个过程中的顺序是有讲究的，因为后画的物体会覆盖之前的物体。

这之后我们改变了一下`Canvas`的绘图上下文的样式并调用`fillText`来绘制文字，也就是记分板那一部分。本游戏没有其他复杂的动画效果和打斗场面，绘制部分大功告成。

## 主循环函数

我们实现了将画面画出来以后，我们紧接着需要实现的就是游戏的循环结构，于是将它放在`main`函数里：

```
// The main game loop
var main = function () {
    var now = Date.now();

    var delta = now - then;
    //console.log(delta);
    update(delta / 1000);
    render();

    then = now;

    // Request to do this again ASAP
    requestAnimationFrame(main);
}; 
```

上面的主函数控制了整个游戏的流程。先是拿到当前的时间用来计算时间差（距离上次主函数被调用时过了多少毫秒）。得到`modifier`后除以 1000(也就是 1 秒中的毫秒数)再传入`update`函数。最后调用`render` 函数并且将本次的时间保存下来。

## 设置 requestAnimationFrame()

在上面的`main`函数中，我们通过`requestAnimationFrame()`调用了`main`函数，所以我们需要声明：

```
var w = window;
requestAnimationFrame = w.requestAnimationFrame || w.webkitRequestAnimationFrame || w.msRequestAnimationFrame || w.mozRequestAnimationFrame; 
```

这里这么多的`||`，不为别的，就是考虑到浏览器兼容问题而已。

## 最后启动游戏

万事具备，只欠东风。到此，所有的游戏代码基本就写完了，我们现在需要做的就是调用相应的函数来启动游戏：

```
// Let's play this game!
var then = Date.now();
reset();
main(); 
```

到这里代码就写完了。先是设置一个初始的时间变量`then`用于首先运行`main`函数使用。然后调用 `reset` 函数来开始新一轮游戏（如果你还记得的话，这个函数的作用是将英雄放到画面中间同时将怪物放到随机的地方以方便英雄去捉它）

用浏览器打开`game.html`，开始玩游戏吧！

## 作业

在玩游戏的过程中，你会发现每一次`hero`捕获到`monster`，`hero`就回到了`canvas`画布的正中间。那么现在需要做的就是，将`hero`在捕捉到`monster`的时候让`hero`就停留在捕获的位置，不再是回到`canvas`正中间。

## 参考文档

本实验课程文档基于博文改编：

【译】http://www.cnblogs.com/Wayou/p/how-to-make-a-simple-html5-canvas-game.html 【原】http://www.lostdecadegames.com/how-to-make-a-simple-html5-canvas-game/