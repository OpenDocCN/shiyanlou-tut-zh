# 第 7 节 AOP - 自动创建 Proxy

## 一、实验说明

### 1.1 实验简介

在前面两节的例子中，在配置文件中，我们必须手动为每一个需要 AOP 的 bean 创建 Proxy bean（ ProxyFactoryBean ）。

这不是一个好的体验，例如，我们想让 DAO 层的所有 bean 都支持 AOP ，以便写 SQL 日志，那么必须手工创建很多的 ProxyFactoryBean ，这样会直接导致 xml 配置文件内容成几何级的倍增，不利于 xml 配置维护。幸运的是，Spring 有两种方法，可以为你自动创建 proxy ，本节我们就将学习如何自动创建它。

### 1.2 实验来源

本课程后续实验基于博客园 leiOOlei 博文制作，原文链接见： [`www.cnblogs.com/leiOOlei/p/3441419.html`](http://www.cnblogs.com/leiOOlei/p/3441419.html) ，感谢原作者 [leiOOlei](http://www.cnblogs.com/leiOOlei) 提供的优质教程。

## 二、 利用 BeanNameAutoProxyCreator 自动创建 proxy

手工创建 ProxyFactoryBean 如下：

```java
<beans 

    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="customerService" class=" com.shiyanlou.spring.aop.advice.CustomerService">
        <property name="name" value="Shiyanlou" />
        <property name="url" value="shiyanlou.com" />
    </bean>

    <bean id="hijackAroundMethodBean" class=" com.shiyanlou.spring.aop.advice.HijackAroundMethod" />

    <bean id="customerServiceProxy" 
        class="org.springframework.aop.framework.ProxyFactoryBean">
        <property name="target" ref="customerService" />
        <property name="interceptorNames">
            <list>
                <value>customerAdvisor</value>
            </list>
        </property>
    </bean>

    <bean id="customerAdvisor"    class="org.springframework.aop.support.NameMatchMethodPointcutAdvisor">
        <property name="mappedName" value="printName" />
        <property name="advice" ref=" hijackAroundMethodBean " />
    </bean>
</beans> 
```

配置完后要得到 customerServiceProxy ，需要如下代码：

```java
CustomerService cust = (CustomerService) appContext.getBean("customerServiceProxy"); 
```

在自动模式中，我们需要创建 BeanNameAutoProxyCreator ，将所有的 bean（通过名字或正则表达式匹配）和 advisor 形成一个独立的单元，配置如下：

```java
<beans 

    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="customerService" class=" com.shiyanlou.spring.aop.advice.CustomerService">
        <property name="name" value="Shiyanlou" />
        <property name="url" value="shiyanlou.com" />
    </bean>

    <bean id="hijackAroundMethodBean" class=" com.shiyanlou.spring.aop.advice.HijackAroundMethod" />

    <bean
    class="org.springframework.aop.framework.autoproxy.BeanNameAutoProxyCreator">
        <property name="beanNames">
            <list>
                <value>*Service</value>
            </list>
        </property>
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

以上配置中只要 bean 的 id 符合 *Service ，就会自动创建 proxy ，所以，可以用以下代码获得 proxy 。

```java
CustomerService cust = (CustomerService) appContext.getBean("customerService"); 
```

运行结果如下：

![此处输入图片的描述](img/document-uid122889labid1941timestamp1469425095120.jpg)

## 三、 利用 DefaultAdvisorAutoProxyCreator 创建 Proxy

这种方式利用 DefaultAdvisorAutoProxyCreator 实现自动创建 Proxy ，此种方式威力巨大，任何匹配 Advisor 的 bean ，都会自动创建 Proxy 实现 AOP ，所以慎用。

```java
<beans 

    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="customerService" class=" com.shiyanlou.spring.aop.advice.CustomerService">
        <property name="name" value="Shiyanlou" />
        <property name="url" value="shiyanlou.com" />
    </bean>

    <bean id="hijackAroundMethodBean" class=" com.shiyanlou.spring.aop.advice.HijackAroundMethod" />

    <bean id="customerAdvisor" class="org.springframework.aop.support.NameMatchMethodPointcutAdvisor">
        <property name="mappedName" value="printName" />
        <property name="advice" ref="hijackAroundMethodBeanAdvice" />
    </bean>

       <bean class="org.springframework.aop.framework.autoproxy.DefaultAdvisorAutoProxyCreator" />

</beans> 
```

以上例子中，xml 中任何 bean ，只要有 method 名字为 printName ，使用以下代码时，都会自动创建 Proxy ，来支持 AOP 。

```java
CustomerService cust = (CustomerService) appContext.getBean("customerService"); 
```

实验结果同上。

## 四、 实验总结

本节我们学习了自动创建 Proxy，它解决了手工创建很多的 ProxyFactoryBean ，导致 xml 配置文件内容复杂，难维护的问题。