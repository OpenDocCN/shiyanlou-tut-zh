# 第 5 节 基础组件（2） - Service

## 一、实验简介

本实验将带你学习又一个 Android 的系统组件——服务（Service）。开发 Service 的步骤与 Activity 类似，因为它本身也是可执行的程序，但允许在后台。在本实验中，你将学习到 Service 的生命周期，并通过一个实例来创建和停止 Service。

### 1\. 知识点

*   Service 的生命周期
*   创建 Service

### 2\. 准备工作

请打开桌面上的 Android Studio，新建一个名为`ServicePractice`的项目，域名填写`shiyanlou.com`（即包名为`com.shiyanlou.servicepractice`。项目最小 SDK 版本（`minSDK`）请选择`Android 5.1`，并选择`Blank Activity`作为模块创建一个 Activity，其它选项使用默认值即可。

等待项目创建完成后，请在 AVD 中创建一台模拟器并启动。

推荐的模拟器参数：

*   硬件平台：Nexus S （屏幕分辨率 480*800）
*   系统版本：Android 5.1.1 armeabli-v7a
*   **请去掉`Use Host GPU 选项`**

模拟器的启动时间约为 6 分钟，这段时间你可以回到 Android Studio 继续学习和编码。

## 二、Service 简介

服务（`Service`）是运行在后台的组件，用于表现一些长期运行的操作而不需要与用户交互。甚至在应用已经被销毁（Destroyed）的情况下也能工作。一个服务实质上有两个状态：

> - `Started`：当一个应用组件（比如一个 Activity）通过调用`startService()`方法启动了服务，我们称之为`Started`。一旦启动了，一个服务就可以持续地在后台运行，甚至启动它的组件已经被销毁了。 > - `Bound`：当一个应用组件通过调用`bindService()`方法将其绑定至一个服务时，我们称之为`Bound`。一个绑定了的服务提了一个“客户端-服务器”模式的接口，这个接口允许组件与服务进行交互、发送请求、获得结果，以及做一些跨进程的通讯（IPC）。

一个服务也是拥有其各个生命周期内的回调方法。你可以实现这些方法，以监控这个服务的状态，并且你可以在合适的生命周期阶段内实现一些功能。

### 1\. Service 的生命周期

下图展示了一个服务的生命周期，左边的路径是指服务由`startService()`方法启动，而右边的是指服务由`bindService()`方法启动。

![此处输入图片的描述](img/dddd16e337a41748faaf0dab0901c6c2.jpg)

### 2\. Service 的创建

为了创建一个服务，你需要创建一个 Java 类。这个类应当继承`Service`基类或者它的一个已有的子类。`Service`基类定义了许多的回调方法，下面给出了其中一些比较重要的部分。有 Activity 相同的是，在实际开发中，你不用实现所有回调方法，但你在学习过程中有必要理解每一个方法的含义，并且逐一实现一下。这样做是为了确保你在应用中用到它们的时候，应用能够如用户所期待的那样正常工作。

> - `onStartCommand()`：当其他组件（比如一个 Activity）需要这个服务启动时，可以调用`startService()`方法，从而让系统调用这个方法。如果你实现了这个方法，你就有责任在该方法工作结束后停止服务。此时你可以调用`stopSelf()`或者`stopService()`方法。 > - `onBind()`：当其他组件想要通过调用`bindService()`来与该服务绑定时，系统会调用该方法。如果你实现了这个方法，你就必须提供一个接口来返回一个`IBinder`对象，从而让客户端(`client`)通过该对象来和服务通讯。大多数情况下你都需要实现这个功能，但是如果你不想允许绑定，你可以返回一个`null`。 > - `onUnbind()`：当所有的客户端（`Client`）不再连接到由该服务发布的特定接口时，系统会调用这个方法。 > - `onRebind()`：在服务预先被通知说，所有在`onUnbind()`方法中的客户端断开连接之后，系统会在新的客户端（`client`）连接到服务之时，调用此方法。 > - `onCreate()`：与 Activity 类似，当服务被`onStartCommand()`或者`onBind()`方法首次启动时，系统会调用该方法。这个调用常被用于第一次的初始化。 > - `onDestroy()`：当服务不再使用且被销毁时，系统会调用该方法。你在服务中应当实现该方法，以用于清理诸如线程、已注册的监听器和接收者（`Receiver`）之类的资源。

下面这段程序展示了一个服务的基本框架，即它的生命周期所包含的方法：

```java
package com.shiyanlou.servicepractice;

import android.app.Service;
import android.os.IBinder;
import android.content.Intent;
import android.os.Bundle;

public class MyService extends Service {

   // 设立一个标志位，指示当 service 被中止后应该以哪种模式来启动
   int flag_startMode;

   // 指示绑定的“客户端”的接口
   IBinder mBinder;     

   // 设立一个标志位，只是 onRebind()方法是否应该被调用
   boolean flag_allowRebind;

   // 当服务正在被创建时会调用此方法
   @Override
   public void onCreate() {

   }

   // 调用 startService()方法时，服务再启动过程中会调用该方法
   @Override
   public int onStartCommand(Intent intent, int flags, int startId) {
      return flat_startMode;
   }

   // 当某个“客户端”通过 bindService()方法与服务绑定时，会调用该方法
   @Override
   public IBinder onBind(Intent intent) {
      return mBinder;
   }

   // 当所有的“客户端”通过 unbindService()方法与服务解绑时，会调用该方法
   @Override
   public boolean onUnbind(Intent intent) {
      return flag_allowRebind;
   }

   // 所有在 onUnbind()方法中的客户端断开连接之后，系统会在新的客户端（client）连接到服务之时，会调用此方法
   @Override
   public void onRebind(Intent intent) {

   }

   //当服务不再使用且被销毁时，系统会调用该方法
   @Override
   public void onDestroy() {

   }
} 
```

## 三、实例

下面通过一个实例来展示怎样创建一个 Android 的服务。请跟随下面的步骤来制作一个 Android 应用。

1.  请使用 Android Studio 来创建一个名为`ServicePractice`的项目，包名为`com.shiyanlou.servicepractice`。
2.  修改`MainActivity.java`文件，在其中添加`startService()`和`stopService()`方法。
3.  在同一个包内，重新创建一个名为`MyService.java`的 java 文件。这个文件将会有 Android 服务相关方法的实现。
4.  使用`<service .../>`标签，在项目的`AndroidManifest.xml`文件中定义这个服务（通常这一步在你使用菜单中的创建功能时已自动完成）。同样，一个应用可以拥有一个或多个服务而不受任何限制。
5.  修改`res/layout/activity_main.xml`文件中的默认内容，在线性布局中添加两个按钮。
6.  实现所有要求的功能，编译并运行这个应用，在模拟器中查看运行的过程，检查其是否与预想的一致。

下面给出了`src/com.shiyanlou.servicepractice/MainActivity.java`文件中的代码。这个文件包含了生命周期中的所有基础方法。我们可以通过添加`startService()`和`stopService()`方法来启动和停止服务。

```java
package com.shiyanlou.servicepractice;

import android.app.Activity;
import android.content.Intent;
import android.os.Bundle;
import android.view.View;

public class MainActivity extends Activity {

    @Override
    public void onCreate(Bundle savedInstanceState) {

        // 这里与一般的 Activity 无异，继承父类的 onCreate()方法
        // 并且也加载了布局
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }

    // onStartService()方法用于启动服务
    public void onStartService(View view) {
        // 这里调用了 startService()方法，需填入待启动服务的“意图”
        // 创建的 Intent 对象中第二个参数即要指向的 Service 类
        startService(new Intent(getBaseContext(), MyService.class));
    }

    // onStopService()方法用于停止服务
    public void onStopService(View view) {
        // 同样需要一个 Intent 对象来作为 stopService()方法的参数
        stopService(new Intent(getBaseContext(), MyService.class));
    }
} 
```

接下来创建一个服务。在项目目录上点击右键，在菜单中依次选择`New->Service->Service`。如下图所示。

![此处输入图片的描述](img/1478ee030d78cf35e7dd7d03f630d729.jpg)

在弹出的对话框中填入服务的名字`MyService`，然后点击`Finish`按钮完成。

![此处输入图片的描述](img/86cbb09ad8252db26044932bf07d4485.jpg)

下面给出了`src/com.shiyanlou.servicepractice/MyService.java`文件中的代码。这个文件可以基于我们的需求来实现了`Service`基类里面的一个或多个方法。现在，我们只需要实现两个方法，即`onStartCommand()`和`onDestroy()`。

```java
package com.shiyanlou.servicepractice;

import android.app.Service;
import android.content.Intent;
import android.os.IBinder;
import android.widget.Toast;

public class MyService extends Service {

    @Override
    public IBinder onBind(Intent arg0) {
        return null;
    }

    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        // 该方法会持续运行直到服务已经被关闭
        // Toast 为屏幕中下方要显示的提示框，你在 UI 一节的项目课中可以看到它的教程
        // 此处我们用 Toast 来向用户显示服务已经启动好了
        Toast.makeText(this, "Service status: started", Toast.LENGTH_LONG).show();

        return START_STICKY;
        // 这里的 START_STICKY 是 onStartCommand(0 方法的常量返回值
        // 希望你能查询手册来了解它指代了什么状态
    }

    @Override
    public void onDestroy() {
        // 继承父类的 onDestroy()方法
        super.onDestroy();

        Toast.makeText(this, "Service status: destroyed", Toast.LENGTH_LONG).show();
    }
} 
```

下面是`AndroidManifest.xml`文件中的内容，你需要用`<service.../>`标签来注册服务。

通过菜单创建 Service 的过程会自动修改该文件。

```java
<?xml version="1.0" encoding="utf-8"?>
<manifest 
    package="com.shiyanlou.servicepractice" >

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme" >
        <activity
            android:name=".MainActivity"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

        <service
            android:name=".MyService"
            android:enabled="true"
            android:exported="true" >
        </service>
    </application>

</manifest> 
```

下面给出了`res/layout/activity_main.xml`文件中的布局，它主要包含了两个按钮。

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
        android:text="Service Practice"
        android:textColor="#000000"
        android:textSize="35dp"/>

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/button_startService"
        android:text="Start Service"
        android:onClick="onStartService"
        android:layout_below="@+id/textView_title"
        android:layout_centerHorizontal="true" />

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Stop Service"
        android:id="@+id/button_stopService"
        android:onClick="onStopService"
        android:layout_below="@+id/button_startService"
        android:layout_alignLeft="@+id/button_startService"
        android:layout_alignStart="@+id/button_startService"
        android:layout_alignRight="@+id/button_startService"
        android:layout_alignEnd="@+id/button_startService" />

</RelativeLayout> 
```

下面给出了`res/values/strings.xml`文件中的内容，这里的内容基本上未做改变，仅供你比对和参考。

```java
<resources>
    <string name="app_name">ServicePractice</string>

    <string name="hello_world">Hello world!</string>
    <string name="action_settings">Settings</string>
</resources> 
```

下面来尝试编译运行这个应用。

点击工具栏上的绿色箭头`Run`按钮，等待应用安装至模拟器。

![此处输入图片的描述](img/61db2352b24f1d66f166eb9fd26e1003.jpg)

应用在模拟器上安装完成后会自动启动，界面如图所示：

![此处输入图片的描述](img/a295577f34d4a0ead8192e9f55e1d1bc.jpg)

点击`START SERVICE`按钮，服务就在后台运行了，只要你不主动关闭，就算当前应用被关闭了，该服务仍然在后台运行。效果如下图所示：

![此处输入图片的描述](img/c1a869ef9edfe60827d1d730f967c803.jpg)

如果想要结束服务，则点击`STOP SERVICE`按钮，服务就会被关闭，如下图所示：

![此处输入图片的描述](img/916495dce0fa6772842a7b725fa45ead.jpg)

至此，实验就完成了。

## 四、实验总结

在本实验中我们主要学习了 Android 四大组件之一的 Service，了解到了它的生命周期，并通过一个项目来创建和停止服务。

实际上，掌握了这个技能后，你就可以开发 Android 中的一个经典的项目——音乐播放器。仔细想一想，是不是很多时候在退出了音乐播放器的界面之后，音乐仍然在播放？这就是 Service 的功劳。

除了自己创建 Service，Android 系统也为开发者们提供了许多的 Service 组件，开发者可以通过系统的 Service 来更好地操作系统。

## 五、作业

请在本节课程学习结束后，尝试使用 Service 来完成更多的事情，比如在后台连续发送多个 Toast 消息。

## 参考文档

*   [Android 官方手册 - Service](http://www.android-doc.com/guide/components/services.html)