# 第 8 节 功能补缺和完善

## 一、实验说明

### 1\. 本节目标

这是我们项目课的最后一节，在本节中我们将完成我们遗留下来的一些问题。到时我们就可以完整运行咱们的日记软件了。

**遗留问题：**

**（1）第二节：**

RegisterGUI 注册按钮鼠标点击事件

```java
register.addMouseListener(new MouseAdapter() {
    public void mouseClicked(MouseEvent e) {
    //这里的事件暂且不处理，日后我们将会完善方法。
    }
}); 
```

LoginGUI 中登陆事件

```java
private void event_login() {
    //这里的登陆事件方法暂不处理，日后补充。
} 
```

**（2）第三节：**

1.  添加 Diary.read() 方法读取日记
2.  添加 Diary.addDiary() 方法建立日记

## 二、RegisterGUI

首先，完成第二节中 RegisterGUI 的注册按钮鼠标点击事件

```java
 register.addMouseListener(new MouseAdapter() {

    public void mouseClicked(MouseEvent e) {
        String name = nametext.getText();//得到 name
        String ID = IDtext.getText();//得到 ID
        String passwd = passwdtext.getText();//得到密码
        //如果检测 ID 返回为 null
       if (Register.checkID(ID) == null) { 
       //如果检测密码返回为 null
        if (Register.checkPasswd(passwd) == null) {
        //注册信息，并且得到返回信息
        String srt = Register.register(name, passwd, ID);

      //提示框，注册成功
            JOptionPane.showMessageDialog(contentPane,srt,"information", JOptionPane.PLAIN_MESSAGE);
        //隐藏当前窗体
        setVisible(false);
        //返回首页
        new IndexGUI().init();
        } else {
        //提示框，输出错误信息            JOptionPane.showMessageDialog(contentPane,Register.checkPasswd(passwd), "ERROR", JOptionPane.ERROR_MESSAGE);
        }
        } else {
        //提示框，输出错误信息                JOptionPane.showMessageDialog(contentPane,Register.checkID(ID), "ERROR",     
        JOptionPane.ERROR_MESSAGE);
                }
        }  
        }); 
```

## 三、LoginGUI

接下来，完成完成第二节中 LoginGUI 的登陆事件 event_login()：

```java
 private void event_login()
    {
        String id=IDtxt.getText(); 
        String passwd=new String(passwordField.getPassword());
        String flag=JDOM.read(id, passwd);
        if(flag.contains("Successful landing"))
        {
            //拆分信息
            String[] bufs=flag.split("/");
            String name=bufs[1];
            //提示框，打印登陆成功
            JOptionPane.showMessageDialog(contentPane, "Welcome："+name,"Welcome",JOptionPane.PLAIN_MESSAGE);
            UsersGUI.init(name);
            setVisible(false);
        }
       else
       {
     //提示框，错误信息               JOptionPane.showMessageDialog(contentPane,flag,"ERROR",JOptionPane.ERROR_MESSAGE);
       }
     } 
```

## 四、读取和建立日记

在第三节中 UserGUI readButton 点击事件中添加 Diary.read() 方法。

```java
//Diary.read()方法读取日记;
Diary.read(file, doc); 
```

在第三节中 UserGUI addButton 添加 Diary.addDiary() 方法。

```java
//调用 Diary.addDiary()方法建立日记

Diary.addDiary(pathname, title, txt); 
```

OK，大功告成。至此，我们的日记软件就算完成了。

## 五、写在最后

我们的一个小型的日记软件就算完成了，当然还有很多不完善的地方。比如日记的加密，用户的增加，删除和修改。或者有的同学想在写日记中增加一点音乐等。这都需要小伙伴们自己去完善我们的日记软件了。

希望大家可以从这个项目中学的自己的东西，或则有什么改进的地方，不满意的地方都可以在 问答 中提出。

[项目完整下载地址](http://pan.baidu.com/s/1kTkofKN)