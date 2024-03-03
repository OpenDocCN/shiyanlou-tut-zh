# 第 3 节 核心配置文件详解 - struts.xml

## 实验简介

学习 Struts 的开发，自然要了解 `struts.xml` 这个最重要的配置文件，每个使用 Struts 开发的项目，你都会使用到它。因此有必要详细介绍它，我们把本节课程放到了 Hello World 之后，避免直接介绍造成的空洞性。有了一个简单项目流程的了解之后，相信你会更加清晰的理解 struts.xml 中的各种配置。

## 一、struts.xml 示例

我们可以看看，Struts 官方提供的示例应用中的 struts.xml 是怎么样的，你可以在 `/home/shiyanlou/struts-2.3.20/apps/struts2-blank/WEB-INF/src/java/` 中找到。

```java
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE struts PUBLIC
    "-//Apache Software Foundation//DTD Struts Configuration 2.3//EN"
    "http://struts.apache.org/dtds/struts-2.3.dtd">

<struts>

    <constant name="struts.enable.DynamicMethodInvocation" value="false" />
    <constant name="struts.devMode" value="true" />

    <package name="default" namespace="/" extends="struts-default">

        <default-action-ref name="index" />

        <global-results>
            <result name="error">/WEB-INF/jsp/error.jsp</result>
        </global-results>

        <global-exception-mappings>
            <exception-mapping exception="java.lang.Exception" result="error"/>
        </global-exception-mappings>

        <action name="index">
            <result type="redirectAction">
                <param name="actionName">HelloWorld</param>
                <param name="namespace">/example</param>
            </result>
        </action>
    </package>

    <include file="example.xml"/>

    <!-- Add packages here -->

</struts> 
```

## 二、constant

我们将根据这个示例 struts.xml 文件，依次讲解。我们主要关心 `<struts>...</struts>` 中的内容，从上到下，首先是 `<constant>` 包含一些属性设置，它可以改变 struts 框架的一些行为。例如示例中的 `struts.enable.DynamicMethodInvocation` 设为 true，表示设置动态方法调用为真，而 `struts.devMode` 表示是否启用开发者模式。

## 三、package

在 struts 中，package 用来管理 action、result、interceptor、interceptor-stack 等配置信息，它的属性如下：

| 属性 | 是否必须 | 含义 | | -------- | -----: | :----: | | name | 是 | 包名，唯一标记，其他 package 可以利用 name 来引用 | | extends | 否 | 设置继承其它 package | | namespace | 否 | package 的命名空间，会改变 url 访问地址 | | abstract | 否 | 是否设置为抽象包 |

*   name：必须唯一，这样其他 package 如果引用本 package 的话，才能找得到。

*   extends：当本 package 继承其他 package 的时候，会继承父 package 的所有配置属性（例如 action、result 等等）；由于 package 的信息获取是安装 struts.xml 文件中的先后顺序进行的，因此父 package 必须在子 package 之前先定义。通常情况下，继承一个“struts-default.xml”的 package，这是 Struts2 默认的 package。

*   namespace：namespace 的配置会改变项目的 url 访问地址，主要是针对比较大型的项目以方便管理 action，因为不同 namespace 中的 action 可以同名，从而解决 action 重名的问题。如果没有指定 namespace，则默认为“”。

## 四、action

| 属性 | 是否必须 | 含义 | | -------- | -----: | :----: | | name | 是 | action 的名称 | | class | 否 | action 对应的 JAVA 类 | | method | 否 | 类中的具体方法 | | converter | 否 | 类型转换器 |

*   class：就是在配置文件中的 action 和 JAVA 代码中的某个类是对应的。

*   method：在该 class 中对应执行 Action 的函数方法，默认是 execute()。

## 五、result

| 属性 | 是否必须 | 含义 | | -------- | -----: | :----: | | name | 否 | Action 对应返回的逻辑响应视图 | | type | 否 | 返回结果的类型 |

*   name：具体来说，就是根据某个返回结果，指定响应逻辑，默认是 success。

*   type：返回结果的类型，默认为 dispatcher。

## 六、default-action-ref

如果找不到项目请求的 action，就会报出 404 错误，而且这种错误不可避免，所以我们可以使用 default-action-ref 来指定一个默认的 action，如果系统出现找不到 action 的情况，机会来调用这个默认的 action。

## 七、global-results

设置 package 范围内的全局响应结果。在多个 action 都返回同一个逻辑视图（通常为某个 jsp 页面）的情况下，可以通过该标签来统一配置。

## 八、global-exception-mapping

配置发生异常时的视图信息。exception-mapping 是控制 action 范围内的，而 global-exception-mapp 是控制 package 范围内的。两个都配置时，exception-mapping 的优先级更高。

| 属性 | 是否必须 | 含义 | | -------- | -----: | :----: | | name | 否 | exception 名称 | | result | 是 | 设置异常相应结果的视图信息 | | exception | 是 | 设置异常类型 |

## 九、include

你还可以使用 `include` 来引入外部配置文件，直接给出 url 即可：

```java
<include file="**/**/***.xml" /> 
```

使用 include 的好处在于，例如当我们开发一个比较大型的项目的时候，配置文件肯定会写一大堆。如果写在一个配置文件里就不好查看和修改，不便于维护；所以使用 include 后可以根据模块、也可以根据功能来划分，这样就比较清晰，方便管理和维护。

其他较为常用的还有 `<interceptor>`拦截器等等。

## 十、小结

> 本次课程我们详细讲解了 struts.xml 配置文件。

## 十一、参考文档

> * [struts.xml 配置详解](http://www.cnblogs.com/fmricky/archive/2010/05/20/1740479.html)