# 第 1 节 反汇编一个简单的 C 程序

实验要求:

1）实验部分（以下命令为实验楼 64 位 Linux 虚拟机环境下适用，32 位 Linux 环境可能会稍有不同） 使用

```
gcc –S –o main.s main.c -m32 
```

命令编译成汇编代码，如下代码中的数字请自行修改以防与他人雷同

```
int g(int x)
{
  return x + 3;
}

int f(int x)
{
  return g(x);
}

int main(void)
{
  return f(8) + 1;
} 
```

2）根据本周所学知识分析汇编代码的工作过程，撰写一篇署名博客，并在博客文章中注明“真实姓名（与最后申请证书的姓名务必一致） + 原创作品转载请注明出处 + 《Linux 内核分析》MOOC 课程 http://mooc.study.163.com/course/USTC-1000029000 ”，博客内容的具体要求如下：

*   题目自拟，内容围绕计算机是如何工作的进行；
*   博客中需要使用实验截图
*   博客内容中需要仔细分析汇编代码的工作过程中堆栈的变化
*   总结部分需要阐明自己对“计算机是如何工作的”理解。

3）请提交博客文章 URL 到网易云课堂 MOOC 平台 [Linux 内核分析 MOOC 课程](http://mooc.study.163.com/course/USTC-1000029000#/info)，编辑成一个链接可以直接点击打开。(学堂在线上的学员注意提交博客 URL 到学堂在线上)