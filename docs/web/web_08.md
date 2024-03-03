# 第 2 节 JavaScript 基本语法（上）

## 实验简介

本节实验介绍 JavaScript 的基础语法，其实大多数编程语言的语法都很类似，所以本节介绍比较简单。

主要涉及 JavaScript 的变量、数据类型、各种运算符。最后留有作业，感受 JavaScript 的动态类型特点。

## 一、变量

### 1.什么是变量

从字面上看，变量是可变的量；从编程角度讲，变量是用于存储数据的存储器，每个变量都有其独有的名字，每个变量都占有一段内存。

在程序当中，直接使用数据值或直接使用数据值的内存地址都不够方便，所以我们用变量的名字来表示对应的数据。

每个变量都有它的变量名，变量类型，变量的作用域。

### 2.JavaScript 中的变量

JavaScript 中的变量规则和其它如 C、Java 等语言类似，建议的变量命名规则：

*   1.由字母、数字、下划线组成，区分大小写
*   2.以字母开头
*   3.变量名不能有空格
*   4.不能使用 JavaScript 中的关键字做变量名

### 3.变量的声明

在 JavaScript 中，变量用 Var 命令做声明：

```js
var test ;    // 声明了一个名为 test 的变量。

var test_2 = "shiyanlou" ;  // 声明一个名为 test_2 的变量，并赋值为“shiyanlou”。 
```

在 JavaScript 中，变量也可以不作声明，而在使用时再根据数据的类型来确其变量的类型，如：

```js
x = 100 ;     // 变量 x 为整数
y = "hello" ; // 变量 y 为字符串
z = True ;    // 变量 z 为布尔型
cars=["Audi","BMW","Volvo"]; // cars 是一个数组 
```

### 4.作用域

与其他语言一样，JavaScript 中的变量同样有全局变量和局部变量之分。

全局变量是定义在所有函数之外的，其作用范围是整段 JavaScript 代码；

而局部变量是定义在函数体之内，只对其该函数是可见，而对其它函数则是不可见的。

## 二、数据类型

### 1.字符串

字符串是存储字符（比如 "shiyanlou"）的变量。

字符串可以是引号中的任意文本，您可以使用单引号或双引号，也可以在字符串中使用引号，只要不匹配包围字符串的引号即可：

```js
var carname="shiyanlou";
var carname='shiyanlou';
var answer="I Love 'shiyanlou'";
var answer='I Love "shiyanlou"'; 
```

### 2.数字

JavaScript 只有一种数字类型。数字可以带小数点，也可以不带：

```js
var x1=34.00;      //使用小数点来写
var x2=34;         //不使用小数点来写 
```

### 3.布尔

布尔只能有两个值：true 或 false：

```js
var x=true
var y=false 
```

### 4.数组

创建一个名为 boys 的数组：

```js
var boys=new Array();
boys[0]="Tom";
boys[1]="Jack";
boys[2]="Alex"; 
```

也可以这样:

```js
var boys=new Array("Tom","Jack","Alex"); 
```

或者这样:

```js
var boys=["Tom","Jack","Alex"]; 
```

## 三、运算符

大多数编程语言的运算符规则都是相似的，JavaScript 与大多数人熟悉的 C、Java 等都很接近。

### 1.算数运算符

```js
| 运算符 | 描述  | 示例 |
|:-------|:------|:-----|
| +      | 加    | x+y  |
| -      | 减    | x-y  |
| *      | 乘    | x*y  |
| /      | 除    | x/y  |
| ++     | 累加  | x++  |
| --     | 累减  | x--  |
| %      | 取余数| x%y  |
```

### 2.比较运算符

比较运算符的基本操作过程是，首先对它的操作数进行比较，再返回一个 true 或 False 值，有８个比较运算符:

< (小于)、> (大于)、<= (小于等于)、>= (大于等于)、== (等于)、!= (不等于)。

### 3.逻辑运算符

![biaoge](img/biaoge.jpg)

## 作业

JavaScript 是一种弱类型（或称动态类型）语言，即变量的类型是不确定的，理解下面四段 JavaScript 代码，运行并观察，探索变量类型的不确定性：

```js
x=5+5;     // 数字 + 数字
document.write(x); 
```

```js
y="6"+"6"; // 字符串 + 字符串
document.write(y); 
```

```js
m=7+"7";  // 数字 + 字符串
document.write(m); 
```

```js
n="8"+8;  // 字符串 + 数字
document.write(n); 
```