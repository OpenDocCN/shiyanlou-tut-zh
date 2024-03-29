# 第 6 节 Spring AOP - Pointcut & Advisor

## 一、实验说明

### 1.1 实验简介

上一个实验中的 Spring AOP Advice 例子里，Class（ CustomerService ）中的全部 method 都被自动的拦截了。

但是大多情况下，你只需要一个方法去拦截一两个 method 。这样就引入了 **Pointcut （切入点）**的概念，它允许你根据 method 的名字去拦截指定的 method 。另外，一个 Pointcut 必须结合一个 Advisor 来使用。

### 1.2 实验来源

本课程后续实验基于博客园 leiOOlei 博文制作，原文链接见： [`www.cnblogs.com/leiOOlei/p/3441419.html`](http://www.cnblogs.com/leiOOlei/p/3441419.html) ，感谢原作者 [leiOOlei](http://www.cnblogs.com/leiOOlei) 提供的优质教程。

## 二、 知识点回顾

在 Spring AOP 中，有 3 个常用的概念，Advices 、 Pointcut 、 Advisor ，解释如下：

*   Advices ：表示一个 method 执行前或执行后的动作。
*   Pointcut ：表示根据 method 的名字或者正则表达式去拦截一个 method 。
*   Advisor ： Advice 和 Pointcut 组成的独立的单元，并且能够传给 proxy factory 对象。

下边来回顾一下上一篇例子中的代码 CustomerService.java 。

> 建议同学们将这个例子再在 Eclipse 中运行起来，若不熟练的话可以返回到上一个实验中再浏览一下相关介绍。

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

配置文件 SpringAOPAdvice.xml ：

```java
<beans 

    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="customerService" class="com.shiyanlou.spring.aop.advice.CustomerService">
        <property name="name" value="shiyanlou" />
        <property name="url" value="shiyanlou.com" />
    </bean>

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

HijackAroundMethod.java ：

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

运行如下 App.java ：

```java
package com.shiyanlou.spring.aop.advice;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class App {

    public static void main(String[] args) {
        ApplicationContext appContext = new ClassPathXmlApplicationContext(
                new String[] { "SpringAOPAdvice.xml" });

        System.out.println("使用 Spring AOP 如下");
        CustomerService cust = (CustomerService) appContext.getBean("customerServiceProxy");
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

运行结果如下：

![此处输入图片的描述](img/document-uid122889labid1940timestamp1469417054584.jpg)

上边的结果中，CustomerService.java 中，全部的 method 方法全部被拦截了，下边我们将展示怎样利用 Pointcuts 只拦截 printName() 。

## 三、 改进

我们可以用名字匹配法和正则表达式匹配法去匹配要拦截的 method 。

### 3.1 Pointcut - Name match example

通过 pointcut 和 advisor 拦截 printName() 方法。创建一个 NameMatchMethodPointcut 的 bean ，将你想拦截的方法的名字 printName 注入到属性 mappedName ，如下：

```java
<bean id="customerPointcut"
        class="org.springframework.aop.support.NameMatchMethodPointcut">
        <property name="mappedName" value="printName" />
</bean> 
```

创建一个 DefaultPointcutAdvisor 的 advisor bean ，将 pointcut 和 advice 关联起来。

```java
<bean id="customerAdvisor"
        class="org.springframework.aop.support.DefaultPointcutAdvisor">
        <property name="pointcut" ref="customerPointcut" />
        <property name="advice" ref=" hijackAroundMethodBean " />
</bean> 
```

更改代理的 interceptorNames 值，将上边的 advisor（ customerAdvisor ）替代原来的 hijackAroundMethodBean 。

```java
<bean id="customerServiceProxy"
        class="org.springframework.aop.framework.ProxyFactoryBean">

        <property name="target" ref="customerService" />

        <property name="interceptorNames">
            <list>
                <value>customerAdvisor</value>
            </list>
        </property>
</bean> 
```

所有的配置文件如下：

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans 

    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd">

     <bean id="customerService" class="com.shiyanlou.spring.aop.advice.CustomerService">
        <property name="name" value="Shiyanlou" />
        <property name="url" value="shiyanlou.com" />
    </bean>

    <bean id="hijackAroundMethodBean" class="com.shiyanlou.spring.aop.advice.HijackAroundMethod" />

    <bean id="customerServiceProxy" class="org.springframework.aop.framework.ProxyFactoryBean">

        <property name="target" ref="customerService" />

        <property name="interceptorNames">
            <list>
                <value>customerAdvisor</value>
            </list>
        </property>
    </bean>

    <bean id="customerPointcut"class="org.springframework.aop.support.NameMatchMethodPointcut">
        <property name="mappedName" value="printName" />
    </bean>

    <bean id="customerAdvisor" class="org.springframework.aop.support.DefaultPointcutAdvisor">
        <property name="pointcut" ref="customerPointcut" />
        <property name="advice" ref="hijackAroundMethodBean" />
    </bean>

</beans> 
```

再运行一下 App.java，输出结果如下：

![此处输入图片的描述](img/document-uid122889labid1940timestamp1469417074224.jpg)

以上运行结果显示，只拦截了 printName() 方法。

注意：以上配置中 pointcut 和 advisor 可以合并在一起配置，即不用单独配置 customerPointcut 和 customerAdvisor ，只要配置 customerAdvisor 时 class 选择 NameMatchMethodPointcutAdvisor 如下：

```java
<bean id="customerAdvisor" class="org.springframework.aop.support.NameMatchMethodPointcutAdvisor">
        <property name="mappedName" value="printName" />
        <property name="advice" ref="hijackAroundMethodBean" />
</bean> 
```

这样，整个配置文件如下：

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans 

    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd">

     <bean id="customerService" class="com.shiyanlou.spring.aop.advice.CustomerService">
        <property name="name" value="Shiyanlou" />
        <property name="url" value="shiyanlou.com" />
    </bean>

    <bean id="hijackAroundMethodBean" class="com.shiyanlou.spring.aop.advice.HijackAroundMethod" />

    <bean id="customerServiceProxy" class="org.springframework.aop.framework.ProxyFactoryBean">
        <property name="target" ref="customerService" />
        <property name="interceptorNames">
            <list>
                <value>customerAdvisor</value>
            </list>
        </property>
    </bean>

    <bean id="customerAdvisor" class="org.springframework.aop.support.NameMatchMethodPointcutAdvisor">
        <property name="mappedName" value="printName" />
        <property name="advice" ref="hijackAroundMethodBean" />
    </bean>

</beans> 
```

实际上这种做法将 method 名字与具体的 advice 捆绑在一起，有悖于 Spring 松耦合理念，如果将 method 名字单独配置成 pointcut（切入点），advice 和 pointcut 的结合会更灵活，使一个 pointcut 可以和多个 advice 结合。

### 3.2 Pointcut - Regular exxpression match example

你可以配置用正则表达式匹配需要拦截的 method ，如下配置：

```java
<bean id="customerAdvisor" class="org.springframework.aop.support.RegexpMethodPointcutAdvisor">
        <property name="patterns">
            <list>
                <value>.*URL.*</value>
            </list>
        </property>
        <property name="advice" ref="hijackAroundMethodBean" />
    </bean> 
```

配置文件详情如下：

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans 

    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="customerService" class="com.shiyanlou.spring.aop.advice.CustomerService">
        <property name="name" value="Shiyanlou" />
        <property name="url" value="shiyanlou.com" />
    </bean>

    <bean id="hijackAroundMethodBean" class="com.shiyanlou.spring.aop.advice.HijackAroundMethod" />

    <bean id="customerServiceProxy" class="org.springframework.aop.framework.ProxyFactoryBean">
        <property name="target" ref="customerService" />
        <property name="interceptorNames">
            <list>
                <value>customerAdvisor</value>
            </list>
        </property>
    </bean>

    <bean id="customerAdvisor" class="org.springframework.aop.support.RegexpMethodPointcutAdvisor">
        <property name="patterns">
            <list>
                <value>.*URL.*</value>
            </list>
        </property>
        <property name="advice" ref="hijackAroundMethodBean" />
    </bean>

</beans> 
```

运行结果：

![此处输入图片的描述](img/document-uid122889labid1940timestamp1469417102363.jpg)

## 四、 实验总结

现在，我们可以拦截名字中包含 URL 字符的 method 了，在实际工作中，你可以用它来管理 DAO 层，例如，你可以用 `.*DAO.*` 来拦截所有 DAO 层中的相关业务。