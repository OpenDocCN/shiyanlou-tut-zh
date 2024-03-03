# 第 3 节 Android UI 编程

## 一、实验简介

在 Android 应用开发的过程中，除了业务逻辑以外，很大一部分内容就是用户界面（UI）的开发。一个拥有良好用户界面的应用，能够得到更多用户的喜爱。本节我们就将来认识 Android 中的 UI 编程体系，学习如何制作界面以及为界面上的控件添加功能。

### 1\. 知识点

*   XML 文件与 UI 界面的关系
*   布局的属性、标识和类型
*   常用 UI 组件相关知识

### 2\. 准备工作

请打开桌面上的 Android Studio，新建一个名为`UIPractice`的项目，域名填写`shiyanlou.com`（即包名为`com.shiyanlou.uipractice`。项目最小 SDK 版本（`minSDK`）请选择`Android 5.1`，并选择`Blank Activity`作为模块创建一个 Activity，其它选项使用默认值即可。

等待项目创建完成后，请在 AVD 中创建一台模拟器并启动。

推荐的模拟器参数：

*   硬件平台：Nexus S （屏幕分辨率 480*800）
*   系统版本：Android 5.1.1 armeabli-v7a
*   **请去掉`Use Host GPU 选项`**

模拟器的启动时间约为 6 分钟，这段时间你可以回到 Android Studio 继续学习和编码。

## 二、认识 UI 编程

相比于 Java 的 Swing 等图形界面开发框架，Android 为开发者提供了更多现成可用的 UI 组件，我们只需要按照其正确的使用方法组装这些 UI 组件，就能形成一个比较完整的用户界面。

那么，在哪里放置这些组件呢？又怎样给组件赋予生命（实现功能）呢？

### 1\. XML 与 UI 界面

Android 中有两种方式可以用来控制界面的视图，一是通过 java 代码，二是通过 XML 文件来定义界面的内容和属性。官方推荐的方式是后者，即通过 XML 文件来控制界面布局。在软件工程学中，这样做也更加符合 MVC 的设计原则，即视图层与其它层分离，实现更低耦合。

在上一节中你已经看到，每个 Activity 中会有如下一行的代码：

```java
setContentView(R.layout.avtivity_main); 
```

这一行代码即为当前 Activity 加载了对应的布局文件。这里的`R`是自动生成的一个常量类，你可以通过它在 java 代码中访问到指定的布局。

同样，如果你需要在 java 代码中访问到某个具体的控件（比如文本框、按钮等），你可以仿照下面这行代码的形式：

```java
Button button = (Button)findViewById(R.id.控件的 ID 值) 
```

这行代码可以是写在某个 Activity 中的，它完成了一个按钮的实例化操作，诞生了一个名为`b`的`Button`对象。`控件的 ID 值`指代的就是 xml 中对应控件的`android:id`属性值，它在 xml 文件中的写法大致是这样的：

```java
<Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/button_someName" /> 
```

程序中只要获得了（即实例化）某个 UI 组件之后，你就可以通过代码来设置它的外观以及行为，可以为它设置监听器等等。

### 2\. Android 官方文档

不管你从事哪一种开发工作，对应领域的开发手册和文档都是必不可少的。教程再多，也不能覆盖到某种开发工具的全部知识。每一年一些技术也会更新，新的技术也会诞生。因此，对于我们每一个开发者来说，仔细阅读将要从事的领域的开发手册和文档都是必不可少的。就 Android 而言，最权威的莫过于官方出品的 Android 文档。

授人以鱼不如授人以渔，希望你在实验楼不仅能学习到如何进行 Android 开发的基础知识，更希望你能够在这儿养成查阅技术文档、手册的好习惯。

在官方手册中，不仅能看到每一种组件（不只是 UI）具有哪些属性、能用调用哪些方法来操作它，还能看到它与其他组件的直接的关系，方便你进行深度的修改。

查看 Android 官方文档的地方除了 Android 官网以外，还能在 Android SDK 的目录中找到。

推荐访问[`www.android-doc.com/`](http://www.android-doc.com/)。

## 三、布局

### 1\. 什么是布局

用户交互界面中最基本的构件块就是`View`(视图)对象。它由`View`类创建，在屏幕上占据了一块矩形区域，并对绘制和事件处理作出相应。`View`类也是各种控件的基类，这些控件有按钮、文本框等等。

`ViewGroup`（视图组）是`View`类的一个子类，提供了不可见的容器，可用于容纳其他的视图、视图组，以以及定义它们的布局特性。

下图展示了布局参数的层次。第三层有许多不同的布局，它们是`ViewGroup`类的子类。一个典型的布局定义了 Android 用户交互界面的视觉结构。它能在程序运行时使用`View`或者`ViewGroup`对象来创建，也能在项目的`res/layout`目录下的`activity_main.xml`文件中声明。

![此处输入图片的描述](img/2e3e9a0e2bfe13b00b6eb630db0c6c1f.jpg)

本课程将更多讲解如何在 XML 文件中，基于布局来创建图形用户界面。一个布局可以包含许多的部件，比如按钮、文本标签、文本框等等。

下面是一个线性布局的 XML 文件的例子：

```java
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout 
   android:layout_width="fill_parent"
   android:layout_height="fill_parent"
   android:orientation="vertical" >

   <TextView android:id="@+id/text"
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:text="This is a TextView" />

   <Button android:id="@+id/button"
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:text="This is a Button" />

   <!-- 更多的 UI 组件从这里开始编辑  -->

</LinearLayout> 
```

只要布局创建好了，你就能在应用的 Activity 代码中加载这些布局资源。

```java
public void onCreate(Bundle savedInstanceState) {
   super.onCreate(savedInstanceState);
   setContentView(R.layout.activity_main);
} 
```

### 2\. 布局的属性

每个布局都有一系列的属性可用于定义其特性。下面是一些常用的属性，每个布局适用的属性不一，具体的你还需要查阅官方文档。

> - `android:id`：为一识别该视图的 ID。 > - `android:layout_width`：布局的宽度。 > - `android:layout_height`：布局的高度。 > - `android:layout_marginTop`：布局顶部的额外空间。 > - `android:layout_marginBottom`：布局底部的额外空间。 > - `android:layout_marginLeft`：布局左部的额外空间。 > - `android:layout_marginRight`：布局右部的额外空间。 > - `android:layout_gravity`：指定子视图的放置方式。 > - `android:layout_weight`：权重。指定了在布局中，应该分配多少的额外空间给某个视图。 > - `android:layout_x`：指定了布局的 X 坐标。 > - `android:layout_y`：指定了布局的 Y 坐标。 > - `android:paddingLeft`：布局左边的内边距。 > - `android:paddingRight`：布局右边的内边距。 > - `android:paddingTop`：布局顶部的内边距。 > - `android:paddingBottom`：布局底部的内边距。

也许你不是很清楚`margin`与`padding`的区别，你可以查看这篇文章——《[Android 的 Margin 和 Padding 属性](http://zhujiao.iteye.com/blog/1856980)》。

上述属性中，宽和高的单位可以是`dp`、`sp`、`pt`、`px`、`mm`和`in`等。《[[Android 问答] px、dp 和 sp，这些单位有什么区别？](http://www.cnblogs.com/bjzhanghao/archive/2012/11/06/2757300.html) 》很好地解释了它们的区别。

更常用的是你可以用下列属性值来度量宽度和高度，此处以宽度的设置为例：

> - `android:layout_width=wrap_content`：此处`wrap_content`能让视图根据自身内容的情况来决定其尺寸。 > - `android:layout_width=fill_parent`：此处`fill_parent`能让视图始终占据其父视图的所有空间。

另外，属性`gravity`在放置视图时也起了很重要的作用，你能使用如下所示的一个或多个属性值（如果是多个请用“`|`”隔开）。

> - `top`：将对象放置在容器的顶端而不改变其尺寸。 > - `bottom`：将对象放置在容器的底端而不改变其尺寸。 > - `left`：将对象放置在容器的左端而不改变其尺寸。 > - `right`：将对象放置在容器的右端而不改变其尺寸。 > - `center_vertical`：将对象放置在容器的垂直方向中心处而不改变其尺寸。 > - `center_horizontal`：将对象放置在容器的水平方向中心处而不改变其尺寸。 > - `fill_vertical`：在需要时增加对象在垂直方向上的尺寸，以完全充满它的容器。 > - `fill_horizontal`：在需要时增加对象在水平方向上的尺寸，以完全充满它的容器。 > - `center`：将对象放置在容器的中心而不改变其尺寸。 > - `fill`：增加对象在水平、垂直方向上的尺寸，以完全填满它的容器。

### 3\. 视图标识

一个视图对象可以像人一样，拥有分配给它的唯一 ID。这个 ID 用于在项目中识别该视图。

在 XML 标签中分配 ID 的写法如下：

```java
android:id="@+id/组件的 ID 名称" 
```

这里的 ID 名称可以是`button_1`之类的字样，其中的`@`和`+`符号的意思解释如下。

*   字符串开头的`@`符号指示了 XML 解析器应该解析和扩展这个 ID 字符串的余下部分，并且将其识别为唯一的 ID 资源。
*   符号`+`的意思是这是一个新的资源名，它必须在项目的资源中被创建和添加。为了创建这个视图对象的实例，以及从布局中捕获它，请参考下列的 java 代码写法。 `//声明一个 Button 对象 Button myButton = (Button)findViewByID(R.id.my_button); //使用 findViewByID()方法来完成组件的实例化过程。`

### 4\. 布局的类型

你可以根据应用的实际需求来选择下列的布局。

1.  线性布局`LinearLayout`：线性布局是一个视图组（`ViewGroup`），能在水平或者垂直的单一方向上将所有子元素排成一行。
2.  相对布局`RelativeLayout`：相对布局是一个将子视图显示在相对的位置上的布局。
3.  表格布局`TableLayout`：表格布局是在行、列中组合子元素的视图。
4.  绝对布局`AbsoluteLayout`：绝对布局能让你指定子元素的精确位置。
5.  帧布局`FrameLayout`：帧布局是一个屏幕上的占位符，你可以用它来显示单一视图。
6.  列表视图`ListView`：列表布局是可以滚动的，是用于显示子元素列表的视图组。
7.  网格视图`GridView`：网格视图是在二维可滚动的网格中显示子元素的视图组。

下面主要对两种布局进行详细说明。

#### （1）线性布局 LinearLayout

线性布局`LinearLayout`中的组件会按照顺序依次排列。根据`android:orientation`属性设置的不同，`LinearLayout`中的组件可以按照水平方向或者竖直方向来排列。需要注意的是，当一行上或者一列上的组件排列到屏幕的边缘时，这一行（或一列）余下的组件则可能不会显示。

设置 LinearLayout 时常用的属性主要有下面几个： > - `android:orientation`:该属性决定了布局管理器内的组件的排列方式，即按照水平方向排列(属性值`horizontal`)或者垂直方向（属性值`vertical`）排列，默认为后者。 > - `android:gravity`:译成中文就是“重力”，该属性决定了布局管理器内的组件的对齐方式。可用选项有`left`、`right`、`top`、`bottom`、`center`等（更多属性请查阅文档），它们可以单独使用，也可以用`|`符合组合起来，比如`top|center_vertical`。对应的 java 代码是`setGravity(int)`。 > - `android:baselineAligned`:该属性决定了布局管理器内的子元素是否与其基线（Base Line）对齐。 > - `android:divider`:该属性决定了布局管理器的排列方式为垂直（`vertical`）时，是否显示子元素之间的分隔条。

在 LinearLayout 中摆放的子元素常用的属性有： > - `android:layout_weight`:这是比较常用的一个属性，它决定了该元素在 LinearLayout 中的所占的权重。例如你想让两个 Button 在同一行中各占 50%的宽度，则可以将两个按钮的宽度都设置为`android:layout_width="0dp"`，再各自辅以这个属性`android:layout_weight=1`，那么无论屏幕宽度是多少，它们都会自动地占据这一行一半的宽度。 > - `android:layout_gravity`:该属性决定了子元素在 LinearLayout 中的对齐方式，相对于`android:gravity`，这是只对当前子元素（比如线性布局中的某个按钮）有效的，而后者是对其包含的所有子元素（线性布局中的所有元素）有效。

#### （2）表格布局——TableLayout

表格布局`TableLayout`与`LinearLayout`类似，也是线性布局的一种，只是它能同时通过行和列的形式来排列组件。如果你需要使用这类布局，你需要往`TableLayout`中添加名为`TableRow`的子组件来控制每一行的内容，需要用多少行就添加多少行。每一行中的组件排列方式则与`LinearLayout`是相同的。

设置 TableLayout 时常用的属性除了`LinearLayout`中的以外，还有以下几个： > - `android:shrinkAllColumns`:该属性设置了能被收缩的列的序号，对应的 java 代码是`setShrinkColumns(boolean)` > - `android:collapseColumns`:对应的 java 代码是`setCollapseColumns(int, boolean)` > - `android:stretchAllColumns`:对应的 java 代码是`setStretchColumns(boolean)`

## 四、常用 UI 组件

在应用的用户交互界面中，输入控件是可交互的组件。Android 提供了数量可观的 UI 控件，比如按钮、文本框、单选框、复选框和滑动条等等。

在 Android Studio 打开项目的某个布局文件时，左侧便会出现这些组件，如下图所示：

![此处输入图片的描述](img/e11e00a10dead3fbc17c5239c652ca38.jpg)

一个视图`View`是将某些东西绘制在屏幕上的对象，用户可与之交互。一个视图组`ViewGroup`是能容纳其他试图（和视图组）的对象，它可以定义用户交互界面的布局。

通常你会在 XML 文件中定义布局。类似于 HTML，XML 文件中提供了布局的可读结构。例如，你可以定义一个带有文本标签`TextView`和按钮`Button`的垂直布局，如下所示：

```java
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout 
   android:layout_width="fill_parent"
   android:layout_height="fill_parent"
   android:orientation="vertical" >

   <TextView android:id="@+id/text"
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:text="I am a TextView" />

   <Button android:id="@+id/button"
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:text="I am a Button" />
</LinearLayout> 
```

### 1\. Android UI 控件

Android 提供了许多的 UI 组件，你能按需构建应用的用户交互界面。

这些控件主要有： > - `TextView`：文本标签，该控件用于显示文本。 > - `EditText`：文本框，是预定义好的 TextView 的一个子类，有富文本编辑能力。 > - `AutoCompleteTextView`：自动完成的文本标签，类似于 EditText。当用户在输入的时候会自动给出一些完整的建议。 > - `Button`：按钮，可以按压、点击，由用户完成一个动作。 > - `ImageButton`：图片按钮，顾名思义。 > - `CheckBox`：复选框，可以作为用户使用的一个开关。当有一系列可供选择的选项时，建议使用该控件。 > - `ToggleButton`：带有一个“灯泡”效果的开关按钮。 > - `RadioButton`：单选按钮，只有选中和未选中状态。 > - `RadioGroup`：是 RadioButton 的集合，在同组内只能选中一个单选按钮。 > - `Spinner`：下拉列表，允许用户在多个选项中选择一个。 > - `TimePicker`：时间选择器，能让用户输入时间，有 12 小时和 24 小时模式。 > - `DatePicker`：日期选择器，能让用户输入日期。 > - `ProgressBar`：进度条，为用户提供某项工作的进度，比如后台正在做的事情。

此外还有很多控件没有提到，你可以查阅官方文档来了解他们。

#### （1）TextView

`TextView`类似于 Java Swing 中的`JLabel`，是用于显示文字信息的一个标签组件。它继承了`View`类，同时是`EditText`和`Button`两个 UI 组件类的父类。

对于功能而言，TextView 其实就是一个不具有编辑功能的文本编辑器。如果你想要创建一个能够编辑内容的文本框，则可以使用`EditText`。

实际上，`CheckedTextView`也继承了 TextView。它多了一个标记`checked`状态的部分，我们可以通过调用`setChecked(boolean)`以及`isChecked()`等方法来改变或获得这个组件的`checked`状态。当然，你还能通过调用`setCheckMarkDrawable()`方法来设置其是够是`checked`（即“被勾选”）。

TextView 在 XML 定义中具有大量的属性，大部分的属性除了适用于 TextView 以外也适用于它的子类。

而常用的属性主要有： > -`android:text`：设置文本标签中要显示的内容。对应的 java 代码是`setText(CharSequence)` > -`android:textSize`：设置文本标签中的文字的字号。对应的 java 代码是`setTextSize(float)`。 > -`android:textColor`：设置文本标签中的文字的颜色。对应的 java 代码是`setTextColor(ColorStateList)`。

更多的属性可以查询 Android 官方手册。如果你对其他属性有任何疑问欢迎在实验楼【问答】版块提出，我们竭力为你解答。

#### （2）Button

如同前文所述，按钮`Button`继承了`TextView`类。正如其名，它就是界面上的一个可供点击的按钮。当按钮被点击时，按钮就会触发一个`onClick()`点击事件，我们会在接下来的实例中介绍如何使用它。

按钮的使用相对而言比较容易，你能够为它添加`android:background`属性，以增加按钮的背景颜色或者选取图片作为按钮的背景。一些常用的不规则形状的按钮就是基于此来制作的，只需要一张不规则图案的 png 背景即可。

实例化一个按钮的过程如下所示，通过调用`findViewById()`方法将按钮的`android:id`属性与 java 类中的`Button`对象联系起来。

```java
Button button = (Button)findViewById(R.id.button) 
```

不理解这段代码有没有关系，在事件处理与内部通信一节会对监听器进行更加详细的说明。

#### （3）EditText

`EditText`是**可编辑**的文本框组件，它继承于`TextView`，因此与后者有很多的相似之处。

`EditText`可以继续沿用`TextView`的各类属性，但最常用的还是`android:inputType`。这个属性指定了文本框的输入类型。`inputType`可以支持较多的输入内容，比如数字、邮件地址、人名、多行文本等等。

以下两个子类则继承了`EditText`类：首先是`AutoCompleteTextView`，即具有自动完成功能的`EditText`；然后是`ExtractEditText`，它并不是一个 UI 组件。它是`EditText`组件在 Android 系统中的底层服务类，能够根据需求来对全屏输入法提供支持。

#### （4）ImageView

`ImageView`是用于显示图片（以及 Drawable 对象）的组件，继承了`View`类。

该组件常用的属性主要有： > - `android:src`：该属性决定了 ImageView 将要显示的图片（或 Drawable 对象），由 ID 指定，比如`android:src=@drawable/picture1`。对应的 java 代码是`setImageResource(int)`。 > - `android:maxWidth`：该属性决定了 ImageView 的最大宽度。对应的 java 代码是`setMaxWidth(int)`。 > - `android:maxHeight`：与上面类似，该属性决定了 ImageView 的最大高度。对应的 java 代码是`setMaxHeight(int)`。 > - `android:adjustViewBounds`：该属性决定了 ImageView 是否可以通过调整自身边界来保持所显示图片的长宽比例，对应的 java 代码是`setAdjustViewBounds(boolean)`。 > - `android:scaleType`：该属性决定了所显示的图片要以何种方式来适应 ImageView 的大小。这里的属性值有`center`（把图片放在 ImageView 的中间，没有缩放效果）、`matrix`（使用 matrix 来控制缩放效果）、`centerInside`（保持长宽比例来缩放图片）等，全部参数请查阅文档。对应的 java 代码是`setScaleType(ImageView.ScaleTyle.XXX)`（XXX 即为属性值）。

除了上述属性，ImageView 也有一些特有的设置方法用于控制其所显示的图片，主要如下： > - `setImageBitmap（Bitmap bitmap）`：通过传入一个 Bitmap 对象来设置 ImageView 要显示的图片。 > - `setImageURI(Uri uri)`：使用图片的 URI（统一资源标识符）来设置 ImageView 要显示的图片。 > - `setImageResource`：使用图片的 ID（在 R 类中被索引）来设置 ImageView 要显示的图片。 > - `setImageDrawable(Drawable drawable)`：通过传入一个 Drawable 对象来设置 ImageView 要显示的图片。关于 Drawable 对象你可以查阅官方文档或[点击此处](http://blog.csdn.net/lonelyroamer/article/details/8148147)来了解更多信息。

另外，图片按钮`ImageButton`是继承了`ImageView`的一类组件，也是可以继续沿用后者的各类属性。一方面作为`Button`，你可以为其设置监听器；另外方面作为一个`ImageView`组件，你可以设置这个按钮上要显示哪种图片。

### 2\. 创建 UI 控件

如前文所述，一个视图对象可以拥有分配给它的唯一 ID。分配 ID 的写法如下：

```java
android:id="@+id/button_1" 
```

这里的`button_1`可修改为实际的控件的 ID 值。

接着需要在 XML 文件中指定它的属性，如下所示：

```java
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout 
   android:layout_width="fill_parent" 
   android:layout_height="fill_parent"
   android:orientation="vertical" >

   <Button android:id="@+id/button_1"
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:text="I am a button" />
</LinearLayout> 
```

最后在 Activity 的代码中需要创建一个控件的实例，将其从布局中捕获出来，写法如下：

```java
Button button = (Button)findViewById(R.id.button_1); 
```

## 五、布局和 UI 组件应用实例

最后我们通过一个实例来简单地应用下布局和 UI 组件。

主要有以下步骤：

1.  请使用 Android Studio 来创建一个名为`UIPractice`的项目，包名为`com.shiyanlou.uipractice`。
2.  在`res/layout/activity_main.xml`文件中的布局里，添加三个主要的控件：一个文本框`EditText`、一个按钮`Button`和一个文本标签`TextView`，并设置它们的属性。
3.  修改`MainActivity.java`文件，在`MainActivity`类中声明刚刚添加的控件，在`onCreate()`方法内对它们进行实例化（使用`findViewById（）`方法），对按钮以匿名内部类的方式添加点击事件监听器，在`onClick`方法中实现文本的获取、显示以及颜色的改变效果。
4.  检查代码，编译并运行这个应用，在模拟器中查看运行的过程，检查其是否与设计的相一致。

下面是部分需要用到的代码。

首先是`res/layout/activity_main.xml`中的内容，注意观察这些 UI 组件都用到了哪些属性，以及它们在布局中是怎样确定位置的：

```java
<RelativeLayout 
     android:layout_width="match_parent"
    android:layout_height="match_parent" android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    android:paddingBottom="@dimen/activity_vertical_margin" tools:context=".MainActivity">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/textView_domain"
        android:layout_alignParentTop="true"
        android:layout_centerHorizontal="true"
        android:text="Shiyanlou.com"
        android:textSize="40dp"
        android:textColor="#11AA8C" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/textView_title"
        android:layout_below="@+id/textView_domain"
        android:layout_centerHorizontal="true"
        android:gravity="center"
        android:text="UI Practice"
        android:textColor="#000000"
        android:textSize="35dp"/>

    <EditText
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/editText_name"
        android:layout_below="@+id/textView_title"
        android:layout_marginTop="10dp"
        android:layout_alignParentRight="true"
        android:layout_alignParentEnd="true"
        android:layout_alignParentLeft="true"
        android:layout_alignParentStart="true"
        android:hint="Please enter your name"/>

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/button_showName"
        android:layout_below="@+id/editText_name"
        android:text="Show me!"
        android:layout_marginTop="10dp"
        android:layout_centerHorizontal="true"/>

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_below="@+id/button_showName"
        android:layout_marginTop="10dp"
        android:id="@+id/textView_content"
        android:text="Text will show here."
        android:textSize="30dp"/>

</RelativeLayout> 
```

接下来是`MainActivity.java`中的内容，建议你先按照自己的思路实现，再来通过下面的代码验证自己的想法：

```java
package com.shiyanlou.uipractice;

import android.app.Activity;
import android.graphics.Color;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.TextView;

public class MainActivity extends Activity {

    // 记得要先对待用的组件进行声明
    // 使用 private 修饰符是为了安全性，根据实际情况你可以使用其他修饰符，如 public
    private EditText editText_name;
    private Button button_showName;
    private TextView textView_content;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        // 通过 setContentView 方法来为 Activity 加载布局
        setContentView(R.layout.activity_main);

        // 通过 findViewById()方法来实例化各个组件
        editText_name = (EditText)findViewById(R.id.editText_name);
        button_showName = (Button)findViewById(R.id.button_showName);
        textView_content = (TextView)findViewById(R.id.textView_content);

        // 调用 button_showName 对象的 setOnClickListener()方法来注册监听器
        // 这里的注册方式是匿名内部类，即直接在 setOnClickListener()方法的参数中，
        // 声明一个点击事件监听器，并实现 onClick()事件处理者里面的逻辑

        button_showName.setOnClickListener(new View.OnClickListener() {

            @Override
            public void onClick(View v) {
                // 调用 editText_name 对象的 getText()方法来获取文本框中的输入内容
                // 再用 toString()方法将输入内容转为 String 格式
                String name = editText_name.getText().toString();

                // 获取到输入值后，将其用 textView_content 对象的 setText()方法显示出来
                textView_content.setText(name);

                // 还可以调用它的 setTextColor()方法来改变文本的颜色
                textView_content.setTextColor(Color.RED);
            }
        });

    }

} 
```

检查一下，编译并运行这个应用。等待应用安装完成后，你应该看到如下画面：

![此处输入图片的描述](img/f06915f34a16aa1f9a40039497f3a131.jpg)

尝试在文本框中输入一些内容，比如说你的名字，然后点击`SHOW ME!`按钮，下方的文本内容就会被改变，如图所示。

![此处输入图片的描述](img/de7ca8d1dd5346c4146bc29226860876.jpg)

## 六、实验总结

由于 Android 中 UI 组件种类繁多，本节课程没有逐一对它们进行讲解。但只要掌握了它们的一般使用方法，就能很快迁移到其他的新的乃至自定义的 UI 组件。

希望你能在本课程学习结束后，继续在实验环境中参考开发文档，再动手试一试、写一写其他的 UI 组件，例如`TimePicker`、`ScrollView`等。

## 七、作业

请在本节课程学习结束后，继续学习以下项目课：

1.  《[Android 小案例 - 对话框集锦](https://www.shiyanlou.com/courses/378)》
2.  《[Android 小案例 - 通知](https://www.shiyanlou.com/courses/377)》

## 参考文档

*   [TextView 之二：常用属性](http://blog.csdn.net/jediael_lu/article/details/13625409)