# 第 6 节 用户注册输入信息的检查

## 一、实验说明

### 1\. 本节目标

本节的任务非常简单，就是在 util 包建立一个工具类，限制和检查用户输入的有效性。

这里会使用到正则表达式。

*   [正则表达式基础](https://www.shiyanlou.com/courses/90)

## 二、具体实现

正则表达式验证的规则：

*   ID 1~8 位数字组成 \d{1,8}
*   passwd 6~15 位数字组成 \d{6,15}

建立 Register.java

```java
 package com.Joke.util;

    import com.Joke.entity.User;

    public class Register {

        static User user = new User();

        public static String checkName(String name) {
            user.setName(name);
            return null;

        }

        public static String checkID(String ID) {
            if (ID.matches("\\d{1,8}")) {
                user.setID(ID);
                return null;
            } else
                return "ID not conform to the rules";
        }

        public static String checkPasswd(String passwd) {
            if (passwd.matches("\\d{6,15}")) {
                user.setPasswd(passwd);
                return null;
            } else
                return "Password not conform to the rules";
        }

        //如果以上验证都没有错误信息返回，则执行写入用户信息
        public static String register(String name,String passwd,String ID) {
            user.setName(name);
            user.setPasswd(passwd);
            user.setID(ID);
            return (JDOM.write(user.getName(), user.getPasswd(),
                    user.getID()));

        }
    } 
```

这样，我们就完成了用户信息的验证。

## 三、下节目标

*   完成日记类的新建和阅读