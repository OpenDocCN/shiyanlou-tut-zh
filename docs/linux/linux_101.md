# 第 1 节 lab0

> **[提示]** > 实验素材来源于 > - [uCoreLab 实验指导书](http://objectkuan.gitbooks.io/ucore-docs/content/) > - [uCoreLab 源代码](https://github.com/chyyuu/ucore_lab) > - [MOOC OS 课程视频](http://www.xuetangx.com/courses/TsinghuaX/30240243X/2015_T1/)　 如有问题需要询问，请访问 > - [课程问答和交流区](https://piazza.com/tsinghua.edu.cn/spring2015/30240243x/home) > 当前 OS 课程实验相关源码已经放在/home/shiyanlou/ucore_lab 目录下

## 0: 看课程视频并阅读实验文档

观看[MOOC OS 课程视频](http://www.xuetangx.com/courses/TsinghuaX/30240243X/2015_T1/)中与 lab0 相关的内容 阅读[uCoreLab 实验指导书](http://objectkuan.gitbooks.io/ucore-docs/content/)中与 lab0 相关的文档 > 假定缺省目录是/home/shiyanlou/ucore_lab 目录下

开始动手实验！

## 1: 了解汇编

Try below command

```
$cd related_info/lab0
$gcc -S -m32 lab0_ex1.c 
```

Then you will get lab_ex1.S. Try to understand the content & relation of C file and S file.

## 2: 用 gdb 调试

Try below command

```
$cd related_info/lab0
$gcc -g -m32 lab0_ex2.c 
```

Then you will get a.out. Try to use gdb to debug lab0_ex2\.

## 3: 掌握指针和类型转换相关的Ｃ编程

对于如下的代码段，

```
...
#define STS_CG32        0xC            // 32-bit Call Gate
#define STS_IG32        0xE            // 32-bit Interrupt Gate

#define SETGATE(gate, istrap, sel, off, dpl) {            \
    (gate).gd_off_15_0 = (uint32_t)(off) &amp; 0xffff;        \
    (gate).gd_ss = (sel);                                \
    (gate).gd_args = 0;                                    \
    (gate).gd_rsv1 = 0;                                    \
    (gate).gd_type = (istrap) ? STS_TG32 : STS_IG32;    \
    (gate).gd_s = 0;                                    \
    (gate).gd_dpl = (dpl);                                \
    (gate).gd_p = 1;                                    \
    (gate).gd_off_31_16 = (uint32_t)(off) >> 16;        \
}

 /* Gate descriptors for interrupts and traps */
 struct gatedesc {
    unsigned gd_off_15_0 : 16;        // low 16 bits of offset in segment
    unsigned gd_ss : 16;            // segment selector
    unsigned gd_args : 5;            // # args, 0 for interrupt/trap gates
    unsigned gd_rsv1 : 3;            // reserved(should be zero I guess)
    unsigned gd_type : 4;            // type(STS_{TG,IG32,TG32})
    unsigned gd_s : 1;                // must be 0 (system)
    unsigned gd_dpl : 2;            // descriptor(meaning new) privilege level
    unsigned gd_p : 1;                // Present
    unsigned gd_off_31_16 : 16;        // high bits of offset in segment
 };

... 
```

如果在其他代码段中有如下语句，

```
unsigned intr;
intr=8;
SETGATE(intr, 0,1,2,3); 
```

请问执行上述指令后， intr 的值是多少？

**具体方法**：Try below command

```
$cd related_info/lab0
gcc -g -m32 lab0_ex3.c 2>&amp;1|tee make.log 
```

If you get gcc's error, try to read make.log and fix the bugs. If gcc successed, then you will get a.out. Try to answer below question.

## 4: 掌握通用链表结构相关的Ｃ编程

用在`related_info/lab0/list.h`中定义的结构和函数来实现一个小应用程序完成一个基于此链表的数据对象的访问操作。 可参考`related_info/lab0/lab0_ex４.c`

## 如果完成了 lab0 的实验要求，恭喜你，可以进入 lab1 了！