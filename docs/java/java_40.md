# 第 3 节 Java 集合框架

* * *

## 实验介绍

　　本实验讲学习 Java 集合框架里的接口、具体类和算法的相关知识

## 一、集合框架的介绍

　　我们在超市买东西的时候，如果没有购物车是不是会很麻烦呢？Java 中集合类是一种工具类，就是像购物车一样的容器，存储任意数量的具有共同属性的对象。

　　我们为什么要用集合呢？一个类的内部有许多相同类型的属性，并且他们的作用与意义是一样的，我们最好用一个类似容器的东西去盛放他们，在类的内部就变得井然有序。所以集合便是在类的内部，对数据进行组织的作用。这样我们便可以简单而快速地搜索大量的条目。有的集合接口，提供了一系列排列有序的元素，并且可以在序列中快速地插入或者删除有关元素。还有一些集合接口，提供了映射关系，可以通过关键字（key）去快速查找到对应的唯一对象，而这个关键字可以是任意类型。

　　集合框架是为表示和操作集合而规定的一种统一的标准的体系结构。任何集合框架都包含三大内容：对外的接口、接口的实现和对集合运算的算法。 　　 ![两大接口](img/document-uid79144labid1102timestamp1435844913664.jpg)　　

## 二、接口

　　因为集合框架中的很多类功能是相似的，所以我们用接口来规范类。Collextion 接口是 java 集合框架里的一个根接口。它也是 List、Set 和 Queue 接口的父接口。Collection 接口中定义了可用于操作 List、Set 和 Queue 的方法——增删改查。

```java
| 方法 | 返回值 | 说明 |
|------|--------|------|
| add(E e) | boolean | 向列表的尾部追加指定的元素（可选操作） |
| add(int index, E element) | void | 在列表的指定位置插入指定元素（可选操作） |
| addAll(Collection<?> c) | boolean | 追加指定 collection 中的所有元素到此列表的结尾，顺序是指定 collection 的迭代器返回这些元素的顺序（可选操作） |
| addAll(int index, Collection<?> c) | boolean | 将指定 collection 中的所有元素都插入到列表中的指定位置（可选操作）|
| clear() | void | 从列表中移除所有元素（可选操作） |
| contains(Object o) | boolean | 如果列表包含指定的元素，则返回 true |
| containsAll(Collection<?> c) | boolean | 如果列表包含指定 collection 的所有元素，则返回 true |
| equals(Object o) | boolean | 比较指定的对象与列表是否相等 |
| get(int index) | <E> | 返回列表中指定位置的元素 |
| hashCode() | int | 返回列表的哈希码 |
| indexOf(Object o) | int | 返回列表中首次出现指定元素的索引，如果列表不包含此元素，则返回 -1 |
| isEmpty() | boolean | 如果列表不包含元素，则返回 true |
| iterator() | Iterator<E> | 返回以正确顺序在列表的元素上进行迭代的迭代器 |
| lastIndexOf(Object o) | int | 返回列表中最后出现指定元素的索引，如果列表不包含此元素，则返回-1 |
| listIterator() | ListIterator<E> | 返回列表中元素的列表迭代器（以正确的顺序） |
| listIterator(int index) | ListIterator<E> | 返回列表中元素的列表迭代器（以正确的顺序），从列表的指定位置开始 |
| remove(Object o) | boolean | 移除列表中出现的首个指定元素(可选操作) |
| removeAll(Collection<?> c) | boolean | 从列表中移除指定 collection 中包含的所有元素（可选操作） |
| retainAll(Collection<?> c) | boolean | 仅在列表中保留指定 collection 中包含的元素（可选操作） |
| set(int index, E element) | E | 用指定元素替换列表中指定位置的元素（可选操作) |
| size() | int | 返回列表中的元素数 |
| subList(int fromIndex, int toIndex) | List<E> | 返回列表中指定的 fromIndex(包括)和 toIndex(不包括)之间的部分视图 |
| toArray() | Object[] | 返回以正确顺序包含列表中的所有元素的数组 |
| toArray() | <T> T[] | 返回以正确顺序包含列表中所有元素的数组；返回数组的运行时类型是指定数组的运行时类型 |
```

　　上面有这么多的方法，我究竟应该怎么运用呢？不要着急，我们在后面讲解具体类的时候，会用到上面的许多方法，到时候我们再来详细地学习，当然如果你不自己动手运用一次，是永远都不可能掌握的。

　　Map 接口也是一个非常重要的集合接口，用于存储键/值对。Map 中的元素都是成对出现的，键值对就像数组的索引与数组的内容的关系一样，将一个键映射到一个值的对象。一个映射不能包含重复的键；每个键最多只能映射到一个值。我们可以通过键去找到相应的值。

![Map 映射](img/document-uid79144labid1102timestamp1436092216199.jpg)

　　value 可以存储任意类型的对象，我们可以根据 key 值快速查找 value。Map 中的键值对以 Entry 类型的对象实例形式存在

看一看 Map 中的方法吧

```java
| 方法 | 返回值 | 说明 |
|------|--------|------|
| clear() | void | 从映射中移除所用映射关系（可选操作） |
| containsKey() | boolean | 如果此映射将一个或多个键映射到指定值，则返回 true |
| entrySet | Set<Map.Entry<K,V>> | 返回此映射中包含的映射关系的 Set 视图 |
| equals(Object o) | boolean | 比较指定的对象与此映射是否相等 |
| get(Object key) | V | 返回指定键所映射的值；如果此映射不包含该键的映射关系，则返回 null |
| hashCode() | int | 返回此映射的哈希码值 |
| isEmpty() | boolean | 如果此映射未包含键值映射关系，则返回 true |
| keySet() | Set<K> | 返回此映射中包含的键的 Set 视图 |
| put(K key, V value) | V | 将指定的值与此映射中的指定键关联（可选操作） |
| putAll(Map<? extends K, ? extends V> m) | void | 从指定映射中将所有映射关系复制到此映射中（可选操作） |
| remove(Object key) | V | 如果存在一个键的映射关系，则将其从此映射中移除（可选操作） |
| size | int | 返回此映射中的键值映射关系数 |
| values() | Collection<V> | 返回此映射中包含的值的 Collection 视图 |
```

## 三、具体类

#### 1、List 接口与 ArrayList 类

　　List 是一个接口，不能实例化，需要一个具体类来实现实例化。List 集合中的对象按照一定的顺序排放，里面的内容可以重复。 List 接口实现的类有：ArrayList（实现动态数组），Vector（实现动态数组），LinkedList（实现链表），Stack（实现堆栈）。

　　今天我们主要来学习　java.util.ArrayList，ArrayList　类实现一个课增长的动态数组，它可以存储不同类型的对象，而数组则只能存放特定数据类型的值。

　　我们通过实际的例子来学习 ArrayList 吧！学校的教务系统会对学生进行统一的管理，每一个学生都会有一个学号和学生姓名，我们在维护整个系统的时候，大多数操作是对学生的添加、插入、删除、修改等操作。

先创建一个学生类：

```java
package com.shiyanlou.test_collection_demo;

/*
 * 学生类
 */

public class Student {

    public String id;
    public String name;
    public Student(String id, String name){
        this.id = id;
        this.name = name;
    }
} 
```

再创建一个学生列表，来管理学生

```java
package com.shiyanlou.test_collection_demo;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.Iterator;
import java.util.List;
/*
 * 管理学生类
 */
public class ListTest {

    /*
     * 用于存放学生的 List
     */
    public List students;
    public ListTest() {
        this.students = new ArrayList();
    }

    /*
     * 用于往 students 中添加学生
     */
    public void testAdd() {
        //创建一个学生对象，并通过调用 add 方法，添加到学生管理 List 中
        Student st1 = new Student("1", "张三");
        students.add(st1);

        //添加到 List 中的类型均为 Object，所以取出时还需要强转
        Student temp = (Student)students.get(0);
        System.out.println("添加了学生：" + temp.id + ":" + temp.name);

        Student st2 = new Student("2","李四");
        students.add(0, st2);
        Student temp2 = (Student)students.get(0);
        System.out.println("添加了学生：" + temp2.id + ":" +temp2.name);

        Student[] student = {new Student("3", "王五"),new Student("4", "马六")};
        students.addAll(Arrays.asList(student));
        Student temp3 = (Student)students.get(2);
        Student temp4 = (Student)students.get(3);
        System.out.println("添加了学生：" + temp3.id + ":" +temp3.name);
        System.out.println("添加了学生：" + temp4.id + ":" +temp4.name);
        Student[] student2 = {new Student("5", "周七"),new Student("6", "赵八")};
        students.addAll(2,Arrays.asList(student2));
        Student temp5 = (Student)students.get(2);
        Student temp6 = (Student)students.get(3);
        System.out.println("添加了学生：" + temp5.id + ":" +temp5.name);
        System.out.println("添加了学生：" + temp6.id + ":" +temp6.name);

    }

    /*
     * 取得 List 中的元素的方法
     */
    public void testGet() {
        int size = students.size();
        for(int i = 0;i<size;i++){
            Student st = (Student)students.get(i);
            System.out.println("学生：" + st.id+":"+ st.name);

        }
    }

    /*
     * 通过迭代器来遍历
     */
    public void testIterator() {
        //通过集合的 iterator 方法，取得迭代器实例
        Iterator it = students.iterator();
        System.out.println("有如下学生（通过迭代器访问）：");
        while(it.hasNext()){

            Student st = (Student)it.next();
            System.out.println("学生" + st.id + ":" +st.name);
        }
    }

    /**
     * 通过 for each 方法访问集合元素
     * @param args
     */
    public void testForEach() {
        System.out.println("有如下学生（通过 for each）：");
        for(Object obj:students){
            Student st = (Student)obj;
            System.out.println("学生：" + st.id + ":" + st.name);
        }
    }

    /**
     * 修改 List 中的元素
     * @param args
     */
    public void testModify(){
        students.set(4,new Student("3","吴酒"));
    }

    /**
     * 删除 List 中的元素
     * @param args
     */
    public void  testRemove() {
        Student st = (Student)students.get(4);
        System.out.println("我是学生：" + st.id + ":" + st.name + "，我即将被删除");
        students.remove(st);
        System.out.println("成功删除学生！");
        testForEach();

    }

    public static void main(String[] args) {
        ListTest lt =  new ListTest();
        lt.testAdd();
        lt.testGet();
        lt.testIterator();
        lt.testModify();
        lt.testForEach();
        lt.testRemove();

    }
} 
```

　　大家看懂了吗？自己实操一下吧。

　　LinkedList 类是用于创建链表数据结构，使用 LinkList 的好处在于它具有访问、检索和删除数据的方法，特别是添加或移除对象时，LinkedList 的表现更佳。但是底层依然必须遍历去查找随机访问的对象，因此性能依然有限。它与 ArrayList 有许多相似之处，在这里便不详细讲解了

#### 2、Set 接口和 HashSet 类

　　Set 接口也是 Collection 接口的子接口，它有一个很重要也是很常用的实现类——HashSet，Set 是元素无序并且不可以重复的集合（List 可以重复），被称为集。

　　接下来我们同样通过代码的形式来详细看一看吧！

　　在上面我们实现了学生的管理，现在学生要做项目，每个项目有一个组长，由组长来组织组员，我们便来实现项目组的管理吧。（接上面的程序实例）

因为项目组的组长由一个老师担任，首先我们来创建一个 PD 类

```java
package com.shiyanlou.test_collection_demo;

import java.util.HashSet;
import java.util.Set;
/*
 * 项目组长类
 */
public class PD {

    public String id;
    public String name;
    //集合后面的<>代表泛型的意思
    //泛型是规定了集合元素的类型
    //我们以后会详细讲到
    public Set<Student> students;
    public PD(String id, String name){
        this.id = id;
        this.name = name;
        this.students = new HashSet<Student>();
    }
} 
```

接下来我们便创建一个 SetTest 类，用来管理项目成员

```java
package com.shiyanlou.test_collection_demo;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.Scanner;

public class SetTest {

    public List<Student> students;

    public SetTest() {
        students = new ArrayList<Student>();
    }

    /*
     * 用于往 students 中添加学生
     */
    public void testAdd() {
        //创建一个学生对象，并通过调用 add 方法，添加到学生管理 List 中
        Student st1 = new Student("1", "张三");
        students.add(st1);

        //添加到 List 中的类型均为 Object，所以取出时还需要强转

        Student st2 = new Student("2","李四");
        students.add(st2);

        Student[] student = {new Student("3", "王五"),new Student("4", "马六")};
        students.addAll(Arrays.asList(student));

        Student[] student2 = {new Student("5", "周七"),new Student("6", "赵八")};
        students.addAll(Arrays.asList(student2));

    }

    /**
     * 通过 for each 方法访问集合元素
     * @param args
     */
    public void testForEach() {
        System.out.println("有如下学生（通过 for each）：");
        for(Object obj:students){
            Student st = (Student)obj;
            System.out.println("学生：" + st.id + ":" + st.name);
        }
    }

    public static void main(String[] args){
        SetTest st = new SetTest();
        st.testAdd();
        st.testForEach();
        PD pd = new PD("1","张老师");
        System.out.println("请：" + pd.name + "选择小组成员！");
        //创建一个 Scanner 对象，用来接收从键盘输入的课程 ID
        Scanner console = new Scanner(System.in);

        for(int i = 0;i < 3; i++){
            System.out.println("请输入学生 ID");
            String studentID = console.next();
            for(Student s:st.students){
                if(s.id.equals(studentID)){
                    pd.students.add(s);
                }
            }
        }
        st.testForEachForSer(pd);
    }
    //打印输出，老师所选的学生！Set 里遍历元素只能用 foreach 和 iterator 
    //不能使用 get() 方法，因为它是无序的，不能想 List 一样查询具体索引的元素
    public void testForEachForSer(PD pd){
        for(Student s: pd.students) {
        System.out.println("选择了学生：" + s.id + ":" + s.name);
        }
    }

} 
```

　　同学们上面的代码看得懂吗？多在 Eclipse 上写一写，然后学会举一反三哦。 　　

#### 3、HashMap 类

　　HashMap 是 Map 的一个重要实现类，也是最常用的，基于哈希表实现。HashMap 中的 Entry 对象那个是无序排列的，Key 值和 value 值都可以为 null，但是一个 HashMap 只能一个 key 值为 null 的映射（key 值不可重复）

　　下面我们通过代码来学习 Map 中的方法吧。同学们都有过选课经历吧，我们就用 Map 来管理课程吧。

创建一个 Course 类：

```java
package com.shiyanlou.test_collection_demo;

public class Course {
    public String id;
    public String name;
    public Course(String id, String name){
        this.id = id;
        this.name = name;
    }
} 
```

创建一个 MapTest 类：

```java
package com.shiyanlou.test_collection_demo;

import java.util.HashMap;
import java.util.Map;
import java.util.Map.Entry;
import java.util.Scanner;
import java.util.Set;

public class MapTest {

    /**
     * 用来承装课程类型对象
     */
    public Map<String, Course> courses;

    /**
     * 在构造器中初始化 courses 属性
     * @param args
     */
    public MapTest() {
        this.courses = new HashMap<String, Course>();
    }

    /**
     * 测试添加：输入课程 ID，判断是否被占用
     * 若未被占用，输入课程名称，创建新课程对象
     * 并且添加到 courses 中
     * @param args
     */
    public void testPut() {
        //创建一个 Scanner 对象，用来获取输入的课程 ID 和名称
        Scanner console = new Scanner(System.in);

        for(int i = 0; i < 3; i++) {
            System.out.println("请输入课程 ID：");
            String ID = console.next();
            //判断该 ID 是否被占用
            Course cr = courses.get(ID);
            if(cr == null){
                //提示输入课程名称
                System.out.println("请输入课程名称：");
                String name = console.next();
                //创建新的课程对象
                Course newCourse = new Course(ID,name);
                //通过调用 courses 的 put 方法，添加 ID-课程映射
                courses.put(ID, newCourse);
                System.out.println("成功添加课程：" + courses.get(ID).name);
            }
            else {
                System.out.println("该课程 ID 已被占用");
                continue;
            }
        }
    }

    /**
     * 测试 Map 的 keySet 方法
     * @param args
     */

    public void testKeySet() {
        //通过 keySet 方法，返回 Map 中的所有键的 Set 集合
        Set<String> keySet = courses.keySet();
        //遍历 keySet，取得每一个键，在调用 get 方法取得每个键对应的 value
        for(String crID: keySet) {
            Course cr = courses.get(crID);
            if(cr != null){
                System.out.println("课程：" + cr.name);
            }
        }
    }

    /**
     * 测试删除 Map 中的映射
     * @param args
     */
    public void testRemove() {
        //获取从键盘输入的待删除课程 ID 字符串
        Scanner console = new Scanner(System.in);
        while(true){
            //提示输出待删除的课程 ID
            System.out.println("请输入要删除的课程 ID！");
            String ID = console.next();
            //判断该 ID 是否对应的课程对象
            Course cr = courses.get(ID);
            if(cr == null) {
                //提示输入的 ID 并不存在
                System.out.println("该 ID 不存在！");
                continue;
            }
            courses.remove(ID);
            System.out.println("成功删除课程" + cr.name);
            break;
        }
    }

    /**
     * 通过 entrySet 方法来遍历 Map
     * @param args
     */
    public void testEntrySet() {
        //通过 entrySet 方法，返回 Map 中的所有键值对
        Set<Entry<String,Course>> entrySet = courses.entrySet();
        for(Entry<String,Course> entry: entrySet) {
            System.out.println("取得键：" + entry.getKey());
            System.out.println("对应的值为：" + entry.getValue().name);
        }
    }

    /**
     * 利用 put 方法修改 Map 中的已有映射
     * @param args
     */
    public void testModify(){
        //提示输入要修改的课程 ID
        System.out.println("请输入要修改的课程 ID：");
        //创建一个 Scanner 对象，去获取从键盘上输入的课程 ID 字符串
        Scanner console = new Scanner(System.in);
        while(true) {
            //取得从键盘输入的课程 ID
            String crID = console.next();
            //从 courses 中查找该课程 ID 对应的对象
            Course course = courses.get(crID);
            if(course == null) {
                System.out.println("该 ID 不存在！请重新输入！");
                continue;
            }
            //提示当前对应的课程对象的名称
            System.out.println("当前该课程 ID，所对应的课程为：" + course.name);
            //提示输入新的课程名称，来修改已有的映射
            System.out.println("请输入新的课程名称：");
            String name = console.next();
            Course newCourse = new Course(crID,name);
            courses.put(crID, newCourse);
            System.out.println("修改成功！");
            break;
        }
    }

    public static void main(String[] args) {
        MapTest mt = new MapTest();
        mt.testPut();
        mt.testKeySet();
        mt.testRemove();
        mt.testModify();
        mt.testEntrySet();

    }
} 
```

## 四、作业

　　好了，同学们今天的内容有一点多，希望大家下来的时候好好消化消化，没有讲到的方法或者类，如果不明白的希望同学们能多去查一查资料，然后在 Eclipse 上操练一下。