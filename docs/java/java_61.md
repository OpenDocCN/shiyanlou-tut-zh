# 第 5 节 Spring AOP - Advice

## 一、实验说明

### 1.1 实验简介

本节实验将学习 Spring AOP 相关知识。

Spring AOP 即 Aspect-oriented programming，面向切面编程，是作为面向对象编程的一种补充，专门用于处理系统中分布于各个模块（不同方法）中的交叉关注点的问题。简单地说，就是一个拦截器（ interceptor ）拦截一些处理过程。

例如，当一 个 method 被执行，Spring AOP 能够劫持正在运行的 method ，在 method 执行前或者后加入一些额外的功能。

在 Spring AOP 中，支持 4 种类型的通知（ Advice ）：

*   Before advice - method 执行前通知
*   After returning advice - method 返回一个结果后通知
*   After throwing advice - method 抛出异常后通知
*   Around advice - 环绕通知，结合了以上三种

### 1.2 实验来源

本课程后续实验基于博客园 leiOOlei 博文制作，原文链接见： [`www.cnblogs.com/leiOOlei/p/3441419.html`](http://www.cnblogs.com/leiOOlei/p/3441419.html) ，感谢原作者 [leiOOlei](http://www.cnblogs.com/leiOOlei) 提供的优质教程。

## 二、一个 Spring AOP 的实例

下边这个例子解释 Spring AOP 怎样工作。首先一个简单的不使用 AOP 的例子。先创建一个简单的 Service ，为了稍后演示，这个类中加了几个简单的打印 method 。

创建一个 Maven 项目，具体步骤不再赘述。创建类 CustomerService.java 如下：

```java
package com.shiyanlou.spring.aop.advice;

public class CustomerService {

    private String name;
    private String url;

    public void setName(String name) {
        this.name = name;
    }

    public void setUrl(String url) {
        this.url = url;
    }

    public void printName() {
        System.out.println("Customer name : " + this.name);
    }

    public void printURL() {
        System.out.println("Customer website : " + this.url);
    }

    public void printThrowException() {
        throw new IllegalArgumentException();
    }

} 
```

Xml 配置文件 SpringAOPAdvice.xml 如下：

```java
<?xml version = "1.0" encoding = "UTF-8"?>
<beans 

    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="customerService" class="com.shiyanlou.spring.aop.advice.CustomerService">
        <property name="name" value="Shiyanlou" />
        <property name="url" value="http://shiyanlou.com" />
    </bean>

</beans> 
```

App.java 编写如下：

```java
package com.shiyanou.spring.aop.advice;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class App {

    public static void main(String[] args) {
        ApplicationContext appContext = new ClassPathXmlApplicationContext(
                new String[] { "SpringAOPAdvice.xml" });

        CustomerService cust = (CustomerService) appContext.getBean("customerService");

        System.out.println("*************************");
        cust.printName();
        System.out.println("*************************");
        cust.printURL();
        System.out.println("*************************");
        try {
            cust.printThrowException();
        } catch (Exception e) {
        }
    }
} 
```

实验结果如图：

![此处输入图片的描述](img/document-uid122889labid1938timestamp1469251099758.jpg)

## 三、 4 种类型的通知（ Advice ）

下面来介绍 4 种类型的通知（ Advice ）

### 3.1 Before Advice

method 运行前，将运行下面的代码

HijackBeforeMethod.java 如下:

```java
package com.shiyanlou.spring.aop.advice;

import java.lang.reflect.Method;
import org.springframework.aop.MethodBeforeAdvice;

public class HijackBeforeMethod implements MethodBeforeAdvice {

    public void before(Method arg0, Object[] args, Object target)
            throws Throwable {
        System.out.println("HijackBeforeMethod : Before method hijacked!");

    }

} 
```

在配置文件中加入新的 bean 配置 HijackBeforeMethod ，然后创建一个新的代理（ proxy ），命名为 customerServiceProxy 。`target` 定义你想劫持哪个 bean； `interceptorNames` 定义想用哪个 class ( advice )劫持 target 。 ApringAOPAdvice.xml 如下：

```java
<beans 

    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="customerService" class="com.shiyanlou.spring.aop.advice.CustomerService">
        <property name="name" value="Shiyanlou" />
        <property name="url" value="http://shiyanlou.com" />
    </bean>

    <bean id="hijackBeforeMethodBean" class="com.shiyanlou.spring.aop.advice.HijackBeforeMethod" />

    <bean id="customerServiceProxy" class="org.springframework.aop.framework.ProxyFactoryBean">
        <property name="target" ref="customerService" />
        <property name="interceptorNames">
            <list>
                <value>hijackBeforeMethodBean</value>
            </list>
        </property>
    </bean>

</beans> 
```

用 Spring proxy 之前，必须添加 CGLIB2 类库，，以下是 pom.xml 依赖：

```java
 <dependency>
        <groupId>org.glassfish.hk2.external</groupId>
        <artifactId>cglib</artifactId>
        <version>2.2.0-b23</version>
    </dependency> 
```

这里 ，我们需要手动添加一下 cglib 的本地 jar 包，打开桌面上的 Xfce ，输入命令：

```java
wget http://labfile.oss.aliyuncs.com/courses/578/cglib-2.2.0-b23.jar 
```

从服务器获取从 cglib 的 jar 包，然后使用 `mvn` 命令进行安装：

```java
mvn install:install-file -Dfile=/home/shiyanlou/cglib-2.2.0-b23.jar -DgroupId=org.glassfish.hk2.external -DartifactId=cglib -Dversion=2.2.0-b23 -Dpackaging=jar 
```

App.java 如下：

```java
package com.shiyanlou.spring.aop.advice;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class App {

    public static void main(String[] args) {
        ApplicationContext appContext = new ClassPathXmlApplicationContext(
                new String[] { "SpringAOPAdvice.xml" });

        CustomerService cust = (CustomerService) appContext.getBean("customerServiceProxy");

        System.out.println("使用 Spring AOP 如下");
        System.out.println("*************************");
        cust.printName();
        System.out.println("*************************");
        cust.printURL();
        System.out.println("*************************");

        try {
            cust.printThrowException();
        } catch (Exception e) {

        }

    }

} 
```

实验结果如下：

![此处输入图片的描述](img/document-uid122889labid1938timestamp1469251116188.jpg)

每一个 customerService 的 method 运行前，都将先执行 HijackBeforeMethod 的 before 方法。

### 3.2 After Returning Advice

创建一个实现了接口 AfterReturningAdvice 的 class ，method 运行后，直到返回结果后，才运行下边的代码，如果没有返回结果，将不运行切入的代码。

HijackAfterMethod.java 如下：

```java
package com.shiyanlou.spring.aop.advice;

import java.lang.reflect.Method;
import org.springframework.aop.AfterReturningAdvice;

public class HijackAfterMethod implements AfterReturningAdvice {

    public void afterReturning(Object returnValue, Method method, Object[] args,
            Object target) throws Throwable {
        System.out.println("HijackAfterMethod : After method hijacked!");

    }

} 
```

修改 bean 配置文件，加入 hijackAfterMethodBean 配置，ApringAOPAdvice.xml 如下：

```java
<beans 

    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="customerService" class="com.shiyanlou.spring.aop.advice.CustomerService">
        <property name="name" value="Shiyanlou" />
        <property name="url" value="shiyanlou.com" />
    </bean>

    <bean id="hijackBeforeMethodBean" class="com.shiyanlou.spring.aop.advice.HijackBeforeMethod" />
    <bean id="hijackAfterMethodBean" class="com.shiyanlou.spring.aop.advice.HijackAfterMethod" />

    <bean id="customerServiceProxy" class="org.springframework.aop.framework.ProxyFactoryBean">
        <property name="target" ref="customerService" />
        <property name="interceptorNames">
            <list>
                <value>hijackAfterMethodBean</value>
            </list>
        </property>
    </bean>

</beans> 
```

现在再运行 App.java 后输出如下：

![此处输入图片的描述](img/document-uid122889labid1938timestamp1469251133089.jpg)

### 3.3 Afetr Throwing Advice

创建一个实现了 ThrowsAdvice 接口的 class ，劫持 IllegalArgumentException 异常，目标 method 运行时，抛出 IllegalArgumentException 异常后，运行切入的方法。HijackThrowException.java 如下：

```java
package com.shiynlou.spring.aop.advice;

import org.springframework.aop.ThrowsAdvice;

import sun.awt.SunToolkit.IllegalThreadException;

public class HijackThrowException implements ThrowsAdvice {

    public void afterThrowing(IllegalArgumentException e) throws Throwable {
        System.out.println("HijackThrowException : Throw exception hijacked!");
    }

} 
```

修改 bean 配置文件，加入了 hijackThrowExceptionBean ，ApringAOPAdvice.xml 如下：

```java
<beans 

    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="customerService" class="com.shiynlou.spring.aop.advice.CustomerService">
        <property name="name" value="Shiyanlou" />
        <property name="url" value="shiyanlou.com" />
    </bean>

    <bean id="hijackBeforeMethodBean" class="com.shiynlou.spring.aop.advice.HijackBeforeMethod" />
    <bean id="hijackAfterMethodBean" class="com.shiynlou.spring.aop.advice.HijackAfterMethod" />
    <bean id="hijackThrowExceptionBean" class="com.shiynlou.spring.aop.advice.HijackThrowException" />

    <bean id="customerServiceProxy" class="org.springframework.aop.framework.ProxyFactoryBean">
        <property name="target" ref="customerService" />
        <property name="interceptorNames">
            <list>
                <value>hijackThrowExceptionBean</value>
            </list>
        </property>
    </bean>

</beans> 
```

运行结果如下：

![此处输入图片的描述](img/document-uid122889labid1938timestamp1469251138872.jpg)

### 3.4 Around Advice

结合了以上 3 种形式的 Advice ，创建一个实现了接口 MethodInterceptor 的 class ，你必须通过 methodInvocation.proceed() 来调用原来的方法，即通过调用 methodInvocation.proceed() 来调用 CustomerService 中的每一个方法，当然也可以不调用原方法 HijackAroundMethod.java 如下：

```java
package com.shiyanlou.spring.aop.advice;

import java.util.Arrays;
import org.aopalliance.intercept.MethodInterceptor;
import org.aopalliance.intercept.MethodInvocation;

public class HijackAroundMethod implements MethodInterceptor {

    public Object invoke(MethodInvocation methodInvocation) throws Throwable {
        System.out.println("Method name : "
                + methodInvocation.getMethod().getName());
        System.out.println("Method arguments : "
                + Arrays.toString(methodInvocation.getArguments()));

        // 相当于  MethodBeforeAdvice
        System.out.println("HijackAroundMethod : Before method hijacked!");

        try {
            // 调用原方法，即调用 CustomerService 中的方法
            Object result = methodInvocation.proceed();

            // 相当于 AfterReturningAdvice
            System.out.println("HijackAroundMethod : After method hijacked!");

            return result;

        } catch (IllegalArgumentException e) {
            // 相当于 ThrowsAdvice
            System.out.println("HijackAroundMethod : Throw exception hijacked!");
            throw e;
        }
    }

} 
```

修改 bean 配置文件，加入了 hijackAroundMethodBean ，ApringAOPAdvice.xml 如下：

```java
<beans 

    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="customerService" class="com.shiyanlou.spring.aop.advice.CustomerService">
        <property name="name" value="Shiyanlou" />
        <property name="url" value="shiyanlou.com" />
    </bean>

    <bean id="hijackBeforeMethodBean" class="com.shiyanlou.spring.aop.advice.HijackBeforeMethod" />
    <bean id="hijackAfterMethodBean" class="com.shiyanlou.spring.aop.advice.HijackAfterMethod" />
    <bean id="hijackThrowExceptionBean" class="com.shiyanlou.spring.aop.advice.HijackThrowException" />
    <bean id="hijackAroundMethodBean" class="com.shiyanlou.spring.aop.advice.HijackAroundMethod" />

    <bean id="customerServiceProxy" class="org.springframework.aop.framework.ProxyFactoryBean">
        <property name="target" ref="customerService" />
        <property name="interceptorNames"> 
            <list>
                <value>hijackAroundMethodBean</value>
            </list>
        </property>
    </bean>
</beans> 
```

运行结果：

![此处输入图片的描述](img/document-uid122889labid1938timestamp1469251145832.jpg)

### 四、 实验总结

CustomerService 中每一个方法的调用，都会执行 HijackAroundMethod 中的 invoke 方法，可以看到整个切入点将目标 around 。

大多数的 Spring 开发者只用 Around Advice ，因为它能够实现所有类型的 Advice 。

在实际的项目开发中，我们还是要尽量选择适合的 Advice 。

在以上的例子中，CustomerService 中的所有方法都被自动拦截，但是大多数情况下，我们不需要拦截一个 class 中的所有方法，而是拦截符合条件的方法。

这时，我们就需要用到 Pointcut and Advice ，即切入点和通知，以后的章节中会逐渐介绍。