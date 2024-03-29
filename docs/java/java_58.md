# 第 2 节 HelloShiyanlou 与松耦合的实现

## 一、实验说明

### 1.1 实验简介

本节实验将带你入门简单的 Maven 项目创建和如何实现松耦合。

#### 1.2 实验来源

本课程后续实验基于[博客园 leiOOlei 博文](http://www.cnblogs.com/leiOOlei/p/3441419.html)制作，内容由原作者 [leiOOlei](http://www.cnblogs.com/leiOOlei) 授权实验楼使用。

## 二、 HelloShiyanlou

### 2.1 实验环境介绍

#### 2.1.1 Maven 简介

在项目开始之前，我们先来了解一下 Maven 相关知识。

>Maven 是一个项目管理和综合工具。Maven 提供了开发人员构建一个完整的生命周期框架。开发团队可以自动完成项目的基础工具建设，Maven 使用标准的目录结构和默认构建生命周期。

>在多个开发团队环境时，Maven 可以设置按标准在非常短的时间里完成配置工作。由于大部分项目的设置都很简单，并且可重复使用，Maven 让开发人员的工作更轻松，同时创建报表，检查，构建和测试自动化设置。

Maven 提供了开发人员的方式来管理：

*   Builds
*   Documentation
*   Reporting
*   Dependencies
*   SCMs
*   Releases
*   Distribution
*   mailing list

概括地说，Maven 简化和标准化项目建设过程。处理编译，分配，文档，团队协作和其他任务的无缝连接。 Maven 增加可重用性并负责建立相关的任务。

#### 2.1.2 Maven 环境搭建

请打开实验环境桌面上的 Xfce ，输入：

```java
wget http://labfile.oss.aliyuncs.com/courses/33/apache-maven-3.3.9-bin.tar.gz 
```

获取 Maven 最新安装包。 然后解压，继续输入：

```java
tar -zvxf apache-maven-3.3.9-bin.tar.gz 
```

接下来，部署，继续输入：

```java
sudo gedit /etc/profile 
```

用 gedit 编辑器打开 profile 文件，修改 Path 。

在 profile 文件的最后输入：

```java
#set Maven environment
export MAVEN_HOME=/home/shiyanlou/apache-maven-3.3.9
export PATH=${MAVEN_HOME}/bin:${PATH} 
```

设置好 MAVEN_HOME 和 PATH 之后，退出 vim 的编辑模式。

然后输入：

```java
source /etc/profile 
```

使操作立即生效。

至此，此 Maven 安装完成，继续输入：

```java
mvn -v 
```

显示如图，则安装成功。

![此处输入图片的描述](img/document-uid122889labid1923timestamp1468914274469.jpg)

#### 2.1.3 导入 Maven 项目所需的包

由于 Maven 新建项目需要很多包，首先会扫描本地仓库，如果没有则需要联网，在中央仓库下载到本地仓库。我们已经把本次项目需要的包已经打包传到服务器上，现在，我们只需要从实验楼下载所要的包，并安放到正确的位置即可。

仍旧在 Xfce 中，输入：

```java
wget http://labfile.oss.aliyuncs.com/courses/33/res.tar.gz 
```

然后，解压：

```java
tar -zvxf res.tar.gz 
```

解压完成后，生成的 .M2 文件夹和之前我们解压的 apache-maven-3.3.9 同在目录： `/home/shiyanlou/` 下。已知，默认的 maven 本地仓库在 `/home/shiyanlou/.m2/` 下。（作业：由何处可以看出本地仓库的默认路径？）所以我们解压好的文件正好覆盖源 `.m2` 文件夹。 `.m2` 文件夹的默认是隐藏文件夹，若要查看，请点击 `视图` ，点击： `显示隐藏文件` 。如图：

![此处输入图片的描述](img/document-uid122889labid1923timestamp1468914339187.jpg)

### 2.2 HelloWorld

#### 2.2.1 创建 Maven 工程

打开 eclipse ，鼠标右键新建，other ，如图：

![此处输入图片的描述](img/document-uid122889labid1923timestamp1468914370121.jpg)

在里面可以找到 Maven 选项卡，点击 Maven Project ， 点击 Next ，如图：

![此处输入图片的描述](img/document-uid122889labid1923timestamp1468914608642.jpg)

选择默认的工程位置如图：

![此处输入图片的描述](img/document-uid122889labid1923timestamp1468914380738.jpg)

点击 Next。

创建普通的 maven 工程，选择如图：

![此处输入图片的描述](img/document-uid122889labid1923timestamp1468914387936.jpg)

点击 Next 。

填写：

*   Group Id：项目的组织机构，也是包的目录结构，一般都是域名的倒序，比如 `com.shiyanlou.demo` ；
*   Atifact Id ：项目实际的名字，比如 spring3-Example ；
*   Packaging ：可在 Group Id 后加 `.Atifact Id`；
*   Version ：项目版本号比如 `1.0-SNAPSHOT`。

这三个选项对应于自动生成的 pom 文件中相应的配置。如图：

![此处输入图片的描述](img/document-uid122889labid1923timestamp1468914454348.jpg)

最后点击 Finish ，创建完成。

### 2.3 代码编写

#### 2.3.1 创建 pom.xml

打开项目的 pom.xml ，进行 Spring 部署，如图：

![此处输入图片的描述](img/document-uid122889labid1923timestamp1468914544694.jpg)

实际上，Maven 使用这三个值来唯一指定一个项目。引用一个库文件也是通过这三个值来确定所引用的库及其版本号，比如本例引用最新的 junit 库版本为 3.8.1\. 此时假定 junit 还需引用其它库， Maven 自动管理而无需在 pom 文件中指定。

#### 2.3.2 创建 HelloWorld.java

接下来，我们在项目名上右键再创建一个 Spring bean，首先创建一个包，包名为 `com.shiyanlou.demo.helloworld` :

![此处输入图片的描述](img/document-uid122889labid1923timestamp1468916343406.jpg)

![此处输入图片的描述](img/document-uid122889labid1923timestamp1468916351546.jpg)

再在该包内创建一个类，类名为 HelloWorld:

![此处输入图片的描述](img/document-uid122889labid1923timestamp1468916375626.jpg)

![此处输入图片的描述](img/document-uid122889labid1923timestamp1468916382322.jpg)

并将 HelloWorld.java 编辑如下：

```java
package com.shiyanlou.demo.helloworld;

public class HelloWorld{

  private String name;

  public void setName(String n){
    this.name=n;
  }

  /*
   *创建打印 Hello 方法
   */
  public void printHello(){
  System.out.println("The first Spring 3:hello"+name);

  }
} 
```

#### 2.3.3 创建 SpringBeans.xml

现在可以开始创建 Spring Bean 配置文件，创建文件 SpringBeans.xml ，配置 bean 如下。文件位于 `src/main/resources` 下。

![此处输入图片的描述](img/document-uid122889labid1923timestamp1468916500795.jpg)

![此处输入图片的描述](img/document-uid122889labid1923timestamp1468917905178.jpg)

![此处输入图片的描述](img/document-uid122889labid1923timestamp1468917857781.jpg)

编辑 SpringBeans.xml 文件如下：

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans 

    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

    <bean id="helloBean" class="com.shiyanlou.demo.helloworld.HelloWorld">
        <property name="name" value="shiyanlou" />
    </bean>

</beans> 
```

#### 2.3.4 创建测试 App

在 `com.shiyanlou.demo.spring3_Example` 包下创建一个类，类名为 App:

```java
package com.shiyanlou.demo.spring3_Example;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class App {

    private static ApplicationContext context;

    public static void main( String[] args )
    {
        context = new ClassPathXmlApplicationContext("SpringBeans.xml");

        HelloWorld obj = (HelloWorld) context.getBean("helloBean");
        obj.printHello();
    }

} 
```

#### 2.3.5 作为 java application 运行：

![此处输入图片的描述](img/document-uid122889labid1923timestamp1468919536018.jpg)

结果如下：

![此处输入图片的描述](img/document-uid122889labid1923timestamp1468917932521.jpg)

至此，helloworld 程序便成功完成！

## 三、松耦合的实现

### 3.1 实验目的

上一部分，我们已经创建了 Maven 项目，打印出了 HelloShiyanlou 。为了方便，我使用上面的工程， pom.xml 文件一致，不必修改。下面，我们实验证明 Spring 的松耦合。假设项目需要输出到 CVS 或者 JSON 。实验效果如图： 输出 CVS :

![此处输入图片的描述](img/document-uid122889labid1923timestamp1468984452028.jpg)

输出 JSON :

![此处输入图片的描述](img/document-uid122889labid1923timestamp1468984458048.jpg)

### 3.2 代码编写

#### 3.2.1 IOutputGenerator.java

创建 `com.shiyanlou.demo.loosely_coupled` 包，新建一个一个 IOutputGenerator 接口:

![此处输入图片的描述](img/document-uid122889labid1923timestamp1468984003950.jpg)

![此处输入图片的描述](img/document-uid122889labid1923timestamp1468984019074.jpg)

接口内容如下：

```java
package com.shiyanlou.demo.loosely_coupled;

public interface IOutputGenerator {
    public void generateOutput();

} 
```

#### 3.2.2 CsvOutputGenerator.java

CVS 输出，实现了 IOutputGenerator 接口。同样的步骤，步骤如下：

![此处输入图片的描述](img/document-uid122889labid1923timestamp1468984158107.jpg)

![此处输入图片的描述](img/document-uid122889labid1923timestamp1468984170766.jpg)

内容如下：

```java
package com.shiyanlou.demo.loosely_coupled;

public class CsvOutputGenerator implements IOutputGenerator {

    public void generateOutput() {
        System.out.println("Creating CsvOutputGenerator  Output......");
    }

} 
```

#### 3.2.3 JsonOutputGenerator.java

JSON 输出，实现了 IOutputGenerator 接口，步骤如下：

![此处输入图片的描述](img/document-uid122889labid1923timestamp1468984212223.jpg)

内容如下：

```java
package com.shiyanlou.demo.loosely_coupled;

public class JsonOutputGenerator implements IOutputGenerator {

    public void generateOutput() {
        System.out.println("Creating JsonOutputGenerator  Output......");
    }

} 
```

### 3.3 用 Spring 依赖注入调用输出

用 Spring 的松耦合实现输出相应的格式。
首先在 `com.shiyanlou.demo.loosely_coupled` 包内创建一个需要用到输出的类 OutputHelper.java ，内容如下：

```java
package com.shiyanlou.demo.loosely_coupled;

public class OutputHelper {
    IOutputGenerator outputGenerator;

    public void generateOutput(){
        this.outputGenerator.generateOutput();
    }

    public void setOutputGenerator(IOutputGenerator outputGenerator){
        this.outputGenerator = outputGenerator;
    }
} 
```

### 3.4 创建一个 spring 配置文件

此文件用于依赖管理 `src/main/resources` 下创建配置文件 Spring-Output.xml 。步骤如下 ：

![此处输入图片的描述](img/document-uid122889labid1923timestamp1468984392631.jpg)

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans 

    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

     <bean id="OutputHelper" class="com.shiyanlou.demo.loosely_coupled.OutputHelper">
        <property name="outputGenerator" ref="CsvOutputGenerator" />
    </bean>

    <bean id="CsvOutputGenerator" class="com.shiyanlou.demo.loosely_coupled.CsvOutputGenerator" />
    <bean id="JsonOutputGenerator" class="com.shiyanlou.demo.loosely_coupled.JsonOutputGenerator" />

</beans> 
```

### 3.5 App.java

此文件用于通过 Spring 调用相应的 output ，内容如下：

```java
package com.shiyanlou.demo.loosely_coupled;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class App {
    private static ApplicationContext context;

    public static void main(String[] args){
        context = new ClassPathXmlApplicationContext(new String[] {"Spring-Output.xml"});

        OutputHelper output = (OutputHelper)context.getBean("OutputHelper");
        output.generateOutput();
    }
} 
```

现在，已经实现了松耦合，当需要输出改变时，不必修改任何代码 .java 文件，只要修改 Spring-Output.xml 文件 `<property name="outputGenerator" ref="CsvOutputGenerator" />` 中的 ref 值，就可以实现输出不同的内容，不修改代码就减少了出错的可能性。

实验目录：

![此处输入图片的描述](img/document-uid122889labid1923timestamp1468985368025.jpg)

### 3.6 运行结果

当 Spring-Output 如下时：

```java
 <bean id="OutputHelper" class="com.shiyanlou.demo.loosely_coupled.OutputHelper">
        <property name="outputGenerator" ref="CsvOutputGenerator" />
 </bean> 
```

运行结果为：

![此处输入图片的描述](img/document-uid122889labid1923timestamp1468985387195.jpg)

## 四、实验总结

本节介绍了 HelloWorld 的 spring 简单项目，并简单介绍了松耦合的概念。请尝试完成下面的作业。如果你对本课程有任何疑问或者建议，欢迎到实验楼[问答](https://www.shiyanlou.com/questions/)板块与我们交流。

## 五、 作业

1.  由何处可以看出本地仓库的默认路径？
2.  应修改何处才能使松耦合实验的运行结果如下？

![此处输入图片的描述](img/document-uid122889labid1923timestamp1468985570432.jpg)