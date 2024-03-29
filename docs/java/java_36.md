# 第 4 节 Java 8 新特性（四）

## 一、实验简介

本节内容将讲解如何使用新的 Data/Time API，以及如何使用 Java 8 内置的 Base64 编码相关 API。

### 1.1 知识点

*   Data/Time API
*   Base64 编码

### 1.2 准备工作

**本节实验内容需要配置相关的实验环境。**你可以仿照上节课《Java 8 新特性（一）》中的步骤来进行配置。如果已完成该步骤，请继续学习下面的内容。

## 二、新的 Data/Time API

Java 8 引入了新的 Date 和 Time 的 API，从而修复了一些旧 API 的缺陷。

这些缺陷主要有：：

*   不是线程安全的：`java.util.Data`不是线程安全的，因此开发者在以前用这些 API 的时候必须要注意并发的情况。而新的 API 是不可变的，并且没有`setter`方法。
*   匮乏的设计：默认日期的年从 1900 年开始、月份从 1 开始，并且日期从 0 开始，它们缺乏统一性。旧的 API 对于日期的操作缺乏直接的方法。新的 API 则对于这些操作提供了许多的设定方法。
*   较难的时区处理：之前，开发者需要写大量的代码来解决时区的问题。新的 API 则简化了这个步骤。

Java 8 在`java.time`包内引入了这些新的 API。下面是一些比较重要的类：

*   `Local`：简化的 data/time API，没有时区处理的特性。
*   `Zoned`：定制的 date/time API，用于处理多时区的情况。

### 2.1 一个本地日期时间的例子

Java 8 提供了`LocalDate`、`LocalTime`和`LocalDataTime`类来简化不需要时区的情况下的开发工作。下面我们通过一个例子来学习它们。

实验步骤主要如下：

1.  双击打开桌面上的 Eclipse。
2.  在 Eclipse 界面上的菜单栏中，通过`File`->`New`来创建一个`Java Project`。
3.  为项目取名为`HelloJava8`。
4.  在项目内创建一个名为`com.shiyanlou.java8`的包，并在这个包内添加一个名为`NewFeaturesTester`的类。
5.  在类中输入下方提供的实验代码。
6.  点击工具栏上的运行（Run）按钮，编译运行该项目。

以下是`NewFeaturesTester.java`类中应当输入的代码：

```java
package com.shiyanlou.java8;

import java.time.LocalDate;
import java.time.LocalTime;
import java.time.LocalDateTime;
import java.time.Month;

public class NewFeaturesTester {
   public static void main(String args[]){
      Java8Tester java8tester = new Java8Tester();
      java8tester.testLocalDateTime();
   }

   public void testLocalDateTime(){

      // 获得当前的日期和时间
      LocalDateTime currentTime = LocalDateTime.now();
      System.out.println("current date and time: " + currentTime);

      // 输出当前时间的本地值（本时区）
      LocalDate date1 = currentTime.toLocalDate();
      System.out.println("local date: " + date1);

      Month month = currentTime.getMonth();
      int day = currentTime.getDayOfMonth();
      int seconds = currentTime.getSecond();

      // 由当前时间对象获得各个字段，输出结果
      System.out.println("month: " + month +"day: " + day +"seconds: " + seconds);

      // 由当前时间附带月份和年再输出
      LocalDateTime date2 = currentTime.withDayOfMonth(10).withYear(2012);
      System.out.println("date 2: " + date2);

      // 输出 2016 年圣诞节的日期
      LocalDate date3 = LocalDate.of(2016, Month.DECEMBER, 25);
      System.out.println("date 3: " + date3);

      // 输出新闻联播的开始时间
      LocalTime date4 = LocalTime.of(19, 00);
      System.out.println("date 4: " + date4);

      // 转化为字符串，再输出
      LocalTime date5 = LocalTime.parse("20:15:30");
      System.out.println("date 5: " + date5);
   }
} 
```

编辑完成后，请点击运行按钮。

输出结果如下图所示，具体的时间可能因实际情况而有所不同：

![此处输入图片的描述](img/document-uid162034labid1829timestamp1461315278117.jpg)

### 2.2 一个带时区的 Date/Time API 例子

具有时区功能的日期时间 API 被用于需要考虑时区信息的情况。同样的，我们通过一个例子来学习它们。

请将 Eclipse 中的代码修改为下面的内容：

```java
package com.shiyanlou.java8;

import java.time.ZonedDateTime;
import java.time.ZoneId;

public class NewFeaturesTester {

   public static void main(String args[]){
      NewFeaturesTester tester = new NewFeaturesTester();
      tester.testZonedDateTime();
   }

   public void testZonedDateTime(){

      // 将字符串代表的时区信息转化
      ZonedDateTime date1 = ZonedDateTime.parse("2016-04-20T19:22:15+01:30[Europe/Paris]");
      System.out.println("date1: " + date1);

      // 设定地区 ID 为亚洲的加尔各答（位于印度），并输出
      ZoneId id = ZoneId.of("Asia/Kolkata");
      System.out.println("ZoneId: " + id);

      // 获得系统默认的当前地区并输出
      ZoneId currentZone = ZoneId.systemDefault();
      System.out.println("CurrentZone: " + currentZone);
   }
} 
```

编辑完成后，请点击运行按钮。

输出结果如下图所示：

![此处输入图片的描述](img/document-uid162034labid1829timestamp1461315545933.jpg)

### 2.3 一个枚举计时单位的例子

在`java.time.temporal.ChronoUnit`包内，添加了枚举常量，来代替旧的 API 里用整数值代表的日期值。下面我们还是通过一个例子来学习它们。

请将 Eclipse 中的代码修改为下面的内容：

```java
package com.shiyanlou.java8;

import java.time.LocalDate;
import java.time.temporal.ChronoUnit;

public class NewFeaturesTester {

   public static void main(String args[]){
      NewFeaturesTester tester = new NewFeaturesTester();
      tester.testChromoUnits();
   }

   public void testChromoUnits(){

      // 获得当前的日期并输出
      LocalDate today = LocalDate.now();
      System.out.println("Current date: " + today);

      // 在当前日期的基础上增加两周时间再输出
      LocalDate nextWeek = today.plus(2, ChronoUnit.WEEKS);
      System.out.println("two weeks after now: " + nextWeek);

      // 在当前日期的基础上增加 6 个月的时间再输出
      LocalDate nextMonth = today.plus(6, ChronoUnit.MONTHS);
      System.out.println("6 months after now: " + nextMonth);

      // 在当前日期的基础上增加 5 年的时间再输出
      LocalDate nextYear = today.plus(5, ChronoUnit.YEARS);
      System.out.println("5 years after now: " + nextYear);

      // 在当前日期的基础上增加 20 年的时间再输出（一个 Decade 为 10 年）
      LocalDate nextDecade = today.plus(2, ChronoUnit.DECADES);
      System.out.println("20 years after now: " + nextDecade);
   }
} 
```

编辑完成后，请点击运行按钮。

输出结果如下图所示：

![此处输入图片的描述](img/document-uid162034labid1829timestamp1461315600359.jpg)

### 2.4 一个时间段的例子

在 Java 8 中，还有两个特殊的类用于处理一些特殊的时间问题：

*   `Period`：该类用于处理**日期**相关的时间段
*   `Duration`：该类用于处理**时间**相关的时间段

仍然是通过一个例子来学习它们，请将 Eclipse 中的代码修改为下面的内容：

```java
package com.shiyanlou.java8;

import java.time.temporal.ChronoUnit;
import java.time.LocalDate;
import java.time.LocalTime;
import java.time.Duration;
import java.time.Period;

public class NewFeaturesTester {

   public static void main(String args[]){
      NewFeaturesTester tester = new NewFeaturesTester();
      tester.showPeriod();
      tester.showDuration();
   }

   public void showPeriod(){

      // 获得当前的日期
      LocalDate date1 = LocalDate.now();
      System.out.println("Current date: " + date1);

      // 在当前日期的基础上增加一个月时间
      LocalDate date2 = date1.plus(1, ChronoUnit.MONTHS);
      System.out.println("Next month: " + date2);

      // 用 between 方法计算两个日期直接的间隔（称之为 Period）
      Period period = Period.between(date2, date1);
      System.out.println("Period: " + period);
   }

   public void showDuration(){

      LocalTime time1 = LocalTime.now();
      Duration fiveHours = Duration.ofHours(5);

      LocalTime time2 = time1.plus(fiveHours);

      // 对应的，用 between 方法显示两个时间直接的间隔（称之为 Duration）
      Duration duration = Duration.between(time1, time2);
      System.out.println("Duration: " + duration);
   }
} 
```

编辑完成后，请点击运行按钮。

输出结果如下图所示：

![此处输入图片的描述](img/document-uid162034labid1829timestamp1461315762445.jpg)

### 2.5 一个时间调节器的例子

`TemporalAdjuster`是用于计算日期的数学工具，比如说你可以通过下面的例子，获得“下个月的第二个周日”这样的计算出来的日期。

同样地，请修改 Eclipse 中的代码为下面的内容：

```java
package com.shiyanlou.java8;

import java.time.LocalDate;
import java.time.temporal.TemporalAdjusters;
import java.time.DayOfWeek;

public class NewFeaturesTester {

   public static void main(String args[]){
      NewFeaturesTester tester = new  NewFeaturesTester;
      tester.applyAdjusters();
   }

   public void applyAdjusters(){

      // 获得当前的日期
      LocalDate date1 = LocalDate.now();
      System.out.println("current date: " + date1);

      // 计算下周一的日期并输出
      LocalDate nextMonday = date1.with(TemporalAdjusters.next(DayOfWeek.MONDAY));
      System.out.println("next monday on : " + nextMonday);

      // 获得下个月的第二个周期的日期并输出
      LocalDate firstInYear = LocalDate.of(date1.getYear(),date1.getMonth(), 1);
      LocalDate secondSunday = firstInYear.with(TemporalAdjusters.nextOrSame(DayOfWeek.SUNDAY)).with(TemporalAdjusters.next(DayOfWeek.SUNDAY));
      System.out.println("second sunday of next month : " + secondSunday);
   }
} 
```

编辑完成后，请点击运行按钮。

输出结果如下图所示：

![此处输入图片的描述](img/document-uid162034labid1829timestamp1461315846096.jpg)

### 2.6 向后兼容能力

为了保持向后兼容的能力，原始的`Date`和`Calendar`对象添加了一个`toInstant`方法。该方法可以将其转换为新 API 下的对象。当然，还可以用`ofInstant`方法来获得`LocalDateTime`和`ZonedDataTime`对象。

请修改 Eclipse 中的代码为下面的内容，以学习这些变化。

```java
package com.shiyanlou.java8;

import java.time.LocalDateTime;
import java.time.ZonedDateTime;
import java.util.Date;
import java.time.Instant;
import java.time.ZoneId;

public class NewFeaturesTester {

   public static void main(String args[]){
      NewFeaturesTester tester = new NewFeaturesTester();
      tester.applyBackwardCompatability();
   }

   public void applyBackwardCompatability(){

      // 获得当前日期并输出
      Date currentDate = new Date();
      System.out.println("Current date: " + currentDate);

      // 获得当前日期的实例（以毫秒的形式）
      Instant now = currentDate.toInstant();
      ZoneId currentZone = ZoneId.systemDefault();

      // 用 ofInstant 方法获得实例
      LocalDateTime localDateTime = LocalDateTime.ofInstant(now, currentZone);
      System.out.println("Local date: " + localDateTime);

      ZonedDateTime zonedDateTime = ZonedDateTime.ofInstant(now, currentZone);
      System.out.println("Zoned date: " + zonedDateTime);
   }
} 
```

编辑完成后，请点击运行按钮。

输出结果如下图所示：

![此处输入图片的描述](img/document-uid162034labid1829timestamp1461315884848.jpg)

## 三、Base64 编码

在 Java 8 中，内置了 Base64 编解码相关的特性。我们可以在 Java 8 中使用下面三种类型的 Base64 编解码：

*   简易模式：输出是完全按照`A-Za-z0-9+/`字符集映射的。编码不会自己增加输出行，解码器也不会接受任何超出`A-Za-z0-9+/`范围的内容。
*   URL 模式：输出基于`A-Za-z0-9+/`的映射，但对于 URL 和文件名是安全的。
*   MIME 模式：输出对于 MIME 类型的内容是友好的。如果超过 76 个字符，则会换行输出。，并且换行符`\n`之后会自动添加一个`\r`。如果某行没有`\r`则说明输出的内容已经结束。

### 3.1 Base64 的内部类和方法

Base64 相关的内部类有：

*   `Base64.Encoder`：这是一个静态类。实现了 Base64 的编码功能，格式遵循了 RFC 4648 和 RFC 2045 标准。
*   `Base64.Decoder`：也是一个静态类。实现了 Base64 的解码功能。

相关的方法有：

*   `getEncoder()`：该方法返回一个使用基本 Base64 编码格式的 Encoder 对象。相反的解码方法是`getDecoder()`。
*   `getUrlEncoder()`：该方法返回一个使用 URL 类型的 Base64 编码格式的 Encoder 对象。相反的解码方法是`getUrlDecoder()`。
*   `getMimeEncoder()`：该方法返回一个使用 MIME 类型的 Base64 编码格式的 Encoder 对象。相反的解码方法是`getMimeDecoder()`。

更多的方法你可以查阅 Java 8 的官方手册。

### 3.2 一个 Base64 编解码的例子

请修改 Eclipse 中的代码来学习如何使用 Java 8 的 API，进行 Base64 编解码。

```java
package com.shiyanlou.java8;

import java.util.Base64;
import java.util.UUID;
import java.io.UnsupportedEncodingException;

public class NewFeaturesTester {

   public static void main(String args[]){
      try {

         // 使用基本的 Base64 编码
         String base64encodedString = Base64.getEncoder().encodeToString("Shiyanlou.com".getBytes("utf-8"));
         System.out.println("Basic base64 encoding:" + base64encodedString);

         // 解码并输出结果
         byte[] base64decodedBytes = Base64.getDecoder().decode(base64encodedString);        
         System.out.println("Original content: " + new String(base64decodedBytes, "utf-8"));

         // 使用 URL 类型的 Base64 编码
         base64encodedString = Base64.getUrlEncoder().encodeToString("Shiyanlou.com".getBytes("utf-8"));
         System.out.println("URL base64 encoding:" + base64encodedString);

         // MIME 类型的 Base64 编码
         StringBuilder stringBuilder = new StringBuilder();

         for (int i = 0; i < 10; ++i) {
            stringBuilder.append(UUID.randomUUID().toString());
         }

         byte[] mimeBytes = stringBuilder.toString().getBytes("utf-8");
         String mimeEncodedString = Base64.getMimeEncoder().encodeToString(mimeBytes);
         System.out.println("MIME base64 encoding:" + mimeEncodedString);

      }catch(UnsupportedEncodingException e){
         // 捕获异常并输出
         System.out.println("Exception:" + e.getMessage());
      }
   }
} 
```

编辑完成后，请点击运行按钮。

输出结果如下图所示：

![此处输入图片的描述](img/document-uid162034labid1829timestamp1461315959918.jpg)

## 四、实验总结

本实验从实践角度出发，讲解了新的 Data/Time API 和 Base64 编码。对于整个课程而言，我们节选了最基础也是最重要的几个知识点来进行讲解。而诸如 Nashorn 引擎（JavaScript）、JavaFX、JDBC 等领域则没有涉及。建议你在具有一定开发经验后再通过相关的课程来学习它们。

当然，最全的 Java 8 新功能说明位于 Java 的官方网站，你可以通过阅读《[What's New in JDK 8](http://www.oracle.com/technetwork/java/javase/8-whats-new-2157071.html)》来了解它们。