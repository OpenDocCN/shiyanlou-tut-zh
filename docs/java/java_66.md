# 第 1 节 Struts 简介

## 实验简介

我们本节课程主要介绍【Struts】的相关知识，将会涉及以下内容：

> * 什么是 Struts

*   Struts2 体系结构
*   Struts2 的优点

## 一、什么是 Struts

Struts 是 Apache 软件基金会（ASF）赞助的一个开源项目。它最初是 Jakarta 项目中的一个子项目，并在 2004 年 3 月成为 ASF 的顶级项目。它实现了基于 Java EE Web 应用的 Model-View-Controller（MVC）设计模式的应用框架，其采用的主要技术是 Java Servlet、JSP 以及 Custom Tag Library 等技术，是 MVC 经典设计模式中的一个经典产品。

Struts 直译过来就是 “支柱，枝干” 的意思，它的目的是为了减少程序开发的时间，项目的创建者认为 JSP、Servlet 的存在虽然可以帮助用户解决大部分问题，但是由于它们的编码对项目的开发带来了许多的不方便，可重用性也差，所以 Struts 应运而生，帮助用户在最短的时间内解决这些问题，其作用就像是 “支柱” 一样。

Struts2 是 Struts 的下一代产品。它在 Struts 和 WebWork 的技术基础上进行了合并，产生了全新的 Struts2 框架。Struts2 修复了大量错误和漏洞，并且体系结构和第一代 Struts 存在巨大差别（因为其实 Struts2 主要是由 WebWork 衍生而来），我们接下来的 Struts 开发课程也以 Struts2 为基础。

## 二、 Struts2 体系结构

Struts2 的体系结构大致如下图所示：

![图片描述信息](img/userid46108labid893time1429253632449.jpg)

上图是 Struts2 的体系结构。一个请求在 Struts2 框架中的处理大概会经过以下几个步骤：

*   1、客户端发出一个指向 Servlet 容器（例如 Tomcat）的请求。

*   2、这个请求会经过几个过滤器 Filter（ActionContextCleanUp 可选过滤器、其他 Web 过滤器如 SiteMesh 等），最后到达 FilterDispatcher 过滤器。

*   3、接着 FilterDispatcher 过滤器被调用，FilterDispatcher 询问 ActionMapper 来决定这个请是否需要调用某个 Action。

*   4、如果 ActionMapper 决定需要调用某个 Action，FilterDispatcher 把请求的处理交给 Action 对象的代理（ActionProxy）。

*   5、ActionProxy 通过配置管理器（Configuration Manager）读取框架的相关配置文件（struts.xml 以及它包含的 *.xml 配置文件），找到需要调用的 Action 类。

*   6、找到需要调用的 Action 类后，ActionProxy 会创建一个 ActionInvocation 的实例。

*   7、ActionInvocation 在调用 Action 的过程之前，会先依次调用相关配置拦截器（Intercepter），执行结果返回 结果字符串。

*   8、ActionInvocation 负责查找 结果字符串 对应的 Result，然后执行这个 Result，再返回对应的结果视图（如 JSP 等等）来呈现页面。

*   9、再次调用所用的配置拦截器（调用顺序与第 7 步相反），然后响应（HttpServletResponse）被返回给浏览器。

## 三、 Struts2 的优点

下面列举 Struts2 的一些主要优点。

*   Struts2 是非侵入式设计，即不依赖于 Servlet API 和 Struts API.

*   Struts2 提供了强大的拦截器，利用拦截器可以进行 AOP 编程（面向切面的编程），实现如权限拦截等功能。

*   Struts2 提供了类型转换器，可以很方便地进行类型转换，例如将特殊的请求参数转换成需要的类型。

*   Struts2 支持多种表现层技术，如 JSP、FreeMarker、Vectocity 等。

*   Struts2 的输入验证可以对指定的方法进行验证。

## 四、小结

> 本次课程介绍了【Struts2】的相关知识，为后续的 Struts2 深入课程打下了基础。

## 六、参考文档

> * [Struts](http://zh.wikipedia.org/zh/Struts) > * [Struts2 简介](http://www.cnblogs.com/sunny08/p/4119479.html)