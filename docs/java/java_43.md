# 第 6 节 文件操作与随机访问文件

* * *

## 实验介绍

本实验将学习 java.io 包下的文件操作和随机访问的相关知识

## 一、文件操作

　　java.io 定义的大多数类都是流式操作，但 File 类不是。它直接处理文件和文件系统。File 类没有指定信息怎样从文件读取或向文件存储；它描述了文件本身的属性。File 对象用来获取或处理与磁盘文件相关的信息，例如权限，时间，日期和目录路径。此外，File 还浏览子目录层次结构。Java 中的目录当成 File 对待，它具有附加的属性——一个可以被 list( )方法检测的文件名列表。

先看一看 File 的构造函数：

```java
//根据 parent 抽象路径名和 child 路径名字符串创建一个新 File 实例。 
File(File parent, String child) 

//通过将给定路径名字符串转换为抽象路径名来创建一个新 File 实例       
File(String pathname) 

// 根据 parent 路径名字符串和 child 路径名字符串创建一个新 File 实例
File(String parent, String child) 

//通过将给定的 file: URI 转换为一个抽象路径名来创建一个新的 File 实例
File(URI uri) 
```

例如:

```java
//一个目录路径参数
File f1 = new File("/Users/mumutongxue/");

//对象有两个参数——路径和文件名
File f2 = new File("/Users/mumutongxue/","a.bat");

//指向 f1 文件的路径及文件名
File f3 = new File(f1,"a.bat"); 
```

```java
| 方法 | 说明 |
|------|------|
| boolean canExecute() | 测试应用程序是否可以执行此抽象路径名表示的文件 |
| boolean canRead() | 测试应用程序是否可以读取此抽象路径名表示的文件 |
| boolean canWrite() | 测试应用程序是否可以修改此抽象路径名表示的文件 |
| int compareTo(File pathname) | 按字母顺序比较两个抽象路径名 |
| boolean createNewFile() | 当且仅当不存在具有此抽象路径名指定名称的文件时，不可分地创建一个新的空文件 |
| static File createTempFile(String prefix, String suffix)  | 在默认临时文件目录中创建一个空文件，使用给定前缀和后缀生成其名称 |
| static File createTempFile(String prefix, String suffix, File directory)  | 在指定目录中创建一个新的空文件，使用给定的前缀和后缀字符串生成其名称 |
| boolean delete() | 删除此抽象路径名表示的文件或目录 |
| void deleteOnExit()  | 在虚拟机终止时，请求删除此抽象路径名表示的文件或目录 |
| boolean equals(Object obj)  | 测试此抽象路径名与给定对象是否相等 |
| boolean exists() | 测试此抽象路径名表示的文件或目录是否存在 |
| File getAbsoluteFile() | 返回此抽象路径名的绝对路径名形式 |
| String getAbsolutePath() | 返回此抽象路径名的绝对路径名字符串 |
| File getCanonicalFile() | 返回此抽象路径名的规范形式 |
| String getCanonicalPath() | 返回此抽象路径名的规范路径名字符串 |
| long getFreeSpace() | 返回此抽象路径名指定的分区中未分配的字节数 |
| String getName() | 返回由此抽象路径名表示的文件或目录的名称 
| String getParent() | 返回此抽象路径名父目录的路径名字符串；如果此路径名没有指定父目录，则返回 null |
| File getParentFile() | 返回此抽象路径名父目录的抽象路径名；如果此路径名没有指定父目录，则返回 null |
| String getPath() | 将此抽象路径名转换为一个路径名字符串 |
| long getTotalSpace() | 返回此抽象路径名指定的分区大小 |
| long getUsableSpace() | 返回此抽象路径名指定的分区上可用于此虚拟机的字节数 |
| int hashCode() | 计算此抽象路径名的哈希码 |
| boolean isAbsolute() | 测试此抽象路径名是否为绝对路径名 |
| boolean isDirectory() | 测试此抽象路径名表示的文件是否是一个目录 |
| boolean isFile() | 测试此抽象路径名表示的文件是否是一个标准文件 |
| boolean isHidden() | 测试此抽象路径名指定的文件是否是一个隐藏文件 |
| long lastModified() | 返回此抽象路径名表示的文件最后一次被修改的时间 |
| long length() | 返回由此抽象路径名表示的文件的长度 |
| String[] list() | 返回一个字符串数组，这些字符串指定此抽象路径名表示的目录中的文件和目录 |
| String[] list(FilenameFilter filter) | 返回一个字符串数组，这些字符串指定此抽象路径名表示的目录中满足指定过滤器的文件和目录 |
| File[] listFiles() | 返回一个抽象路径名数组，这些路径名表示此抽象路径名表示的目录中的文件 |
| File[] listFiles(FileFilter filter) | 返回抽象路径名数组，这些路径名表示此抽象路径名表示的目录中满足指定过滤器的文件和目录 |
| File[] listFiles(FilenameFilter filter) | 返回抽象路径名数组，这些路径名表示此抽象路径名表示的目录中满足指定过滤器的文件和目录 |
| static File[] listRoots() | 列出可用的文件系统根 |
| boolean mkdir() | 创建此抽象路径名指定的目录 |
| boolean mkdirs() | 创建此抽象路径名指定的目录，包括所有必需但不存在的父目录 |
| boolean renameTo(File dest) | 重新命名此抽象路径名表示的文件 |
| boolean setExecutable(boolean executable) | 设置此抽象路径名所有者执行权限的一个便捷方法 |
| boolean setExecutable(boolean executable, boolean ownerOnly) | 设置此抽象路径名的所有者或所有用户的执行权限 |
| boolean setLastModified(long time) | 设置此抽象路径名指定的文件或目录的最后一次修改时间 |
| boolean setReadable(boolean readable) | 设置此抽象路径名所有者读权限的一个便捷方法 |
| boolean setReadable(boolean readable, boolean ownerOnly) | 设置此抽象路径名的所有者或所有用户的读权限 |
| boolean setReadOnly() | 标记此抽象路径名指定的文件或目录，从而只能对其进行读操作 |
| boolean setWritable(boolean writable) | 设置此抽象路径名所有者写权限的一个便捷方法 |
| boolean setWritable(boolean writable, boolean ownerOnly) | 设置此抽象路径名的所有者或所有用户的写权限 |
| String toString() | 返回此抽象路径名的路径名字符串 |
| URI toURI() | 构造一个表示此抽象路径名的 file: URI |
| URL toURL() | 已过时。 此方法不会自动转义 URL 中的非法字符。建议新的代码使用以下方式将抽象路径名转换为 URL：首先通过 toURI 方法将其转换为 URI，然后通过 URI.toURL 方法将 URI 装换为 URL |
```

看一看相关的代码吧：

```java
package com.shiyanlou;

import java.io.File;
import java.io.IOException;

public class  FileDemo {
    public static void main(String[] args){
        //这是我电脑上的路径，同学们可以直接在桌上生成文件。桌面路径为"/root/Desktop"
        //构造函数 File(String pathname)
        File f1 =new
        File("/Users/mumutongxue/1.txt");
        //File(String parent,String child)
        File f2 =new File("/Users/mumutongxue","2.txt");
        //separator 跨平台分隔符
        File f3 =new File("/Users"+File.separator+"mumutongxue");
        File f4 =new File(f3,"3.txt");

        try {
             System.out.println(f1);
                //当文件存在时返回 false；不存在时返回 true
                System.out.println(f2.createNewFile());
                //当文件不存在时返回 false
                System.out.println(f3.delete());
        }catch(IOException e) {
                e.printStackTrace();
        }

        //列出磁盘下的文件和文件夹
        File[] files =File.listRoots();
        for(File file:files){
            System.out.println(file);
            if(file.length()>0){
                String[] filenames =file.list();
                for(String filename:filenames){
                    System.out.println(filename);
                }
            }
        }

    }

} 
```

## 二、随机访问文件

　　对于 FileInputStream/FileOutputStream、FileReader/FileWriter 来说，它们的实例都是顺序访问流，即只能进行顺序读/写。而类 RandomAccessFile 则允许文件内容同时完成读和写操作，它直接继承 object，并且同时实现了接口 DataInput 和 DataOutput，提供了支持随机文件操作的方法：

1.  readXXX()或者 writeXXX():如 ReadInt(),ReadLine(),WriteChar(),WriteDouble()等

2.  int skipBytes(int n):将指针向下移动若干字节

3.  length():返回文件长度

4.  long getFilePointer():返回指针当前位置

5.  void seek(long pos):将指针调用所需位置

    　　在生成一个随机文件对象时，除了要指明文件对象和文件名之外，还需要指明访问文件的模式。

我们来看看 RandomAccessFile 的构造函数：

```java
RandomAccessFile(File file,String mode)
RandomAccessFile(String name,String mode) 
```

mode 的取值：

*   `r`只读，任何写操作都讲抛出 IOException
*   `rw`读写，文件不存在时会创建该文件，文件存在是，原文件内容不变，通过写操作改变文件内容。
*   `rws`同步读写，等同于读写，但是任何写操作的内容都被直接写入物理文件，包括文件内容和文件属性
*   `rwd`数据同步读写，等同于读写，但任何内容写操作都直接写到物理文件，但对文件属性内容的修改不是这样

看看代码吧：

```java
package com.shiyanlou;

import java.io.IOException;
import java.io.RandomAccessFile;

public class  FileDemo {
    public static void main(String[] args){

            int data_arr[] = {12, 32, 43, 45, 1, 5};
            try {
                RandomAccessFile randf=new RandomAccessFile("temp.dat","rw");
                for(int i = 0; i < data_arr.length; i++){
                    randf.writeInt(data_arr[i]);
                }
                for(int i = data_arr.length-1 ; i >= 0; i--){
                    //int 数据占 4 个字节
                    randf.seek(i * 4L);
                    System.out.println(randf.readInt());
                }
                randf.close();
            }catch(IOException e){
                System.out.println("File access error" + e);
            }
    }
} 
```

## 三、小结

　　在 java 中有数据传输的地方都用到 I/O 流（通常是文件、网络、内存和标准输入输出等）

　　InputStream 和 OutputStream 是所有字节流的祖先（只有 RandAccessFile 类是一个列外），read 和 write 是它们最基本的方法，读写单位是字节

　　Reader 和 Writer 是所有字符流的祖先，read 和 write 是它们最基本的方法，读写单位是字符。

　　在众多的流对象中，并不是每一种都单独使用，其中过滤流的子类在数据送出去之前做必要的处理。