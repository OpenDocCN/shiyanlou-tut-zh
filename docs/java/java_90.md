# 第 5 节 实现用户的注册和登陆

## 一、实验说明

### 1\. JDOM 简介以及用法

> JDOM 是一种使用 XML（标准通用标记语言下的一个子集） 的独特 Java 工具包。它的设计包含 Java 语言的语法乃至语义。 > 要使用 JDOM 解析 XML 文件，需要下载 JDOM 的包，实验中使用的是 jdom-1.1。解压之后，将 lib 文件夹下的 `*.jar` 文件以及 build 文件夹下的 jdom.jar 拷贝到工程文件夹下，然后就可以使用 JDOM 操作 XML 文件了。

*   [jdom 下载地址](http://labfile.oss.aliyuncs.com/courses/480/jdom-2.0.6.zip)
*   [jdom 解析 xml 文档](http://wuhongyu.iteye.com/blog/361842)

在实验环境中下载 jdom 可以使用下面的方式，打开 Xfce 终端，输入命令：

```java
$ wget http://labfile.oss.aliyuncs.com/courses/480/jdom-2.0.6.zip
$ unzip jdom-2.0.6.zip 
```

### 2\. 本节目标

在本节中，我们将通过 JDOM 解析咱们的 UserInfo.xml 文档，来实现用户的注册和登陆功能模块。

## 二、具体实现

首先将 JDOM 的 jar 包导入到我们的工程中。

新建类 JDOM.java，主要包含了两个方法，write() 和 read() 方法，分别用于将用户信息写入到 xml 文档中和读出用户信息。

```java
 package com.Joke.util;

    import java.io.File;
    import java.io.FileOutputStream;
    import java.io.IOException;
    import java.util.ArrayList;
    import java.util.Iterator;
    import java.util.List;
    import java.util.Map;
    import java.util.TreeMap;

    import org.jdom.Attribute;
    import org.jdom.Document;
    import org.jdom.Element;
    import org.jdom.JDOMException;
    import org.jdom.input.SAXBuilder;
    import org.jdom.output.XMLOutputter;

    public class JDOM {

        //注册用户信息
        public static String write(String n, String p, String id) {
            // TODO Auto-generated method stub

            //UserInfo.xml 文档的路径
            String path = "./UserInfo.xml";
            //将 xml 文档封装成 file
            File file = new File(path);
            //使用默认的 sax 解析器
            SAXBuilder saxBuilder = new SAXBuilder();
            Document doc; //声明 document 文档
            try {
                doc = saxBuilder.build(file);

                //元素对应到 xml 文档中就是标签
                Element root = doc.getRootElement(); //得到根元素
                Element user = new Element("User"); //建立 User 元素
                Element name = new Element("name");//建立 name 元素
                Element passwd = new Element("passwd");//建立 passwd 元素

                /*首先检测 xml 文档中是否已经存在了 ID 号相同的用户，如果不存在才可以继续注册*/
                if (checkID(id, root)) {
                    //将 ID 设置为 user 的属性
                    user.setAttribute(new Attribute("id", id)); 
                    //设置姓名和密码
                    name.setText(n);
                    passwd.setText(p);

                    //将 name，passwd 元素添加到 user 元素下
                    user.addContent(name);
                    user.addContent(passwd);

                    //将 user 元素添加到根元素下
                    root.addContent(user);

                    //输出 xml 文档
                    XMLOutputter out = new XMLOutputter();
                    out.output(doc, new FileOutputStream(file));
                    return "Successful registration";//返回注册成功
                } else
                    //返回 ID 存在信息，重新输入 ID
                    return "ID already exists, please input again";

            } catch (JDOMException e) {
                // TODO Auto-generated catch block
                e.printStackTrace();
            } catch (IOException e) {
                // TODO Auto-generated catch block
                e.printStackTrace();
            }
            return "ERROR";//未知错误

        }

        public static boolean checkID(String id, Element root) {
            // 检测 ID 是否存在
            boolean flag = true;
            @SuppressWarnings("unchecked")
            //得到 User 标签的所有子元素，并加入到 map 集合中
            List<Element> list = root.getChildren("User");
            //迭代检测是否存在 ID
            Iterator<Element> it = list.iterator();
            while (it.hasNext()) {
                Element e = (Element) it.next();
                if (e.getAttributeValue("id").equals(id)) {
                    flag = false;
                }
            }
            return flag;

        }

        //读取 xml 文档用于登录
        public static String read(String id, String passwd) {
            String path = "./UserInfo.xml";
            File file = new File(path);
            SAXBuilder saxBuilder = new SAXBuilder();

            try {
                Document doc = saxBuilder.build(file);
                Element root = doc.getRootElement();

                //取出用户密码和姓名
                String info = getPasswd(root).get(id);
                if (info == null) {
                    return "User does not exist!!";
                }
                String[] buf = info.split("/");

                if (buf[0].equals(passwd)) {
                    return "Successful landing/" + buf[1];
                }

            } catch (JDOMException e) {
                // TODO Auto-generated catch block
                e.printStackTrace();
            } catch (IOException e) {
                // TODO Auto-generated catch block
                e.printStackTrace();
            }
            return "Wrong password!!";
        }

        @SuppressWarnings("unchecked")
        /*将用户的密码和姓名添加到 map 集合中*/
    private static Map<String, String> getPasswd(Element root) {
            Map<String, String> map = new TreeMap<String, String>();//存贮用户信息
            List<Element> list = new ArrayList<Element>();
            //得到 User 标签的所有子元素信息
            list = root.getChildren("User");
            Iterator<Element> it = list.iterator();
            while (it.hasNext()) {
                Element e = it.next();
                String id = e.getAttributeValue("id");
                String passwd = e.getChildText("passwd");
                String name = e.getChildText("name");
                map.put(id, getInfo(passwd, name));
            }

            return map;

        }

        //处理用户密码和信息
        private static String getInfo(String passwd, String name) {

            return passwd + "/" + name;

        }
    } 
```

以上，我们就完成了 JDOM 写入和读取用户信息。这样注册用户就可以持久化保存了。

## 三、下节目标

当然，我们这个程序还是不完善的，因为总有一些 “坏用户” 来挑战程序的容错性，比如在注册时输入一些空字符啊等。所以下节我们将对用户的输入进行一些限制和检查错误。