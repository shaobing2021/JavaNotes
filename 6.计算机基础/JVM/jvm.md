#### 友情链接

JVM笔记：

https://www.cnblogs.com/yanl55555/category/1686360.html

JVM视频：

https://www.bilibili.com/video/BV1PJ411n7xZ?p=11

JMM讲解：

https://www.cnblogs.com/yuxiang1/archive/2019/05/16/10875619.html

浅谈面试题

* 对JVM理解，JAVA8虚拟机和之前的变化更更新
* 是什么是OOM，什么是栈溢出StackOverFlowError?怎么分析
* JVM常见调优参数
* 内存拍照如何抓取，怎么分析Dump文件
* 谈谈JVM类加载器  ：rt-jar
* 双亲委派

### JVM

JVM运行在操作系统之上，操作系统在硬件上面

#### 1.简介

JVM是什么：二进制字节码的运行环境

特点：一次编译，到处运行。自动内存管理，自动垃圾回收功能。

推动JAVA虚拟机从"JAVA的虚拟机"向多语言虚拟机方向发展

虚拟机和java虚拟机大致：系统虚拟机和程序虚拟机，为物理计算机和为执行单个计算机程序而设计

### JVM体系结构

![JVM架构](https://gitee.com/shaobing2021/typora/raw/master/img/20200728100350.png)

#### 结构图理解

本地方法区是特殊的堆

#### 类加载器

作用：加载Class文件，查看二进制编码文件

![image-20200520092344054](https://gitee.com/shaobing2021/typora/raw/master/img/20200728100351.png)



从JDK1.8之后的版本【JDK1.9和JDK1.10】提供有一个【PlatformClassLoader】类加载器，而在JDK1.8以及以前的版本里面提供的加载器为【ExtClassLoader】,因为在JDK的安装目录里面提供又一个ext的目录，开发者可以将【*.jar】文件拷贝到此目录里面，这样就可以直接执行了，但是这样的处理开发并不安全，最初的时候也是不提倡使用的，所以从JDK1.9开始将其彻底废除，同时为了和系统加载器与应用类加载器之间保持设计的平衡，提供有平台类加载器

```
package chapter1;

public class Hello {
    public static void main(String[] args) {
        ClassLoader systemClassLoader = ClassLoader.getSystemClassLoader();
        System.out.println(systemClassLoader);
        System.out.println(systemClassLoader.getParent());
        System.out.println("--------");
        ClassLoader c1 = Hello.class.getClassLoader();
        System.out.println(c1);//AppClassLoader  系统类加载器
        //为什么要有这个加载器：
        System.out.println(c1.getParent());//PlatformClassLoader  平台类/扩展类加载器   \jre\lib\ext   在jdk1.8之前是ExtClassLoader
        System.out.println(c1.getParent().getParent());//null   rt.jar   启动类加载器
    }
}
```

>  双亲委派机制

App--->PlatForm--->Bootstrap(最终执行)

```
package java.lang;
public class String {
    public String toString(){
        return "hello";
    }
    public static void main(String[] args) {
        String s = new String();
        System.out.println(s.getClass().getClassLoader());
        s.toString();
    }
    //报错信息：Error:(1, 1) java: 程序包已存在于另一个模块中: java.base
}

```

说明及优点：

Java的类加载使用双亲委派模式，即一个类加载器在加载类时，先把这个请求委托给自己的父类加载器去执行，如果父类加载器还存在父类加载器，就继续向上委托，直到顶层的启动类加载器。如果父类加载器能够完成类加载，就成功返回，如果父类加载器无法完成加载，那么子加载器才会尝试自己去加载。这种双亲委派模式的好处：

一个可以避免类的重复加载，另外也避免了java的核心API被篡改

#### 沙箱安全机制

* 字节码校验器：比如写代码忘记；会进行校验。确保java文件遵循java语言规范，实现内存保护
* 类加载器：1.双亲委派禁止

#### Native关键字

点start可以看到native关键字

```
package chapter1;
public class Demo {
    public static void main(String[] args) {
        new Thread(()->{
        },"my thread").start();
    }
    //native:说明调用底层C语言的库
    //本地方法栈native method  调用本地接口JNI，使用本地方法库，扩展java使用，融合
    public native void start();
}
```

#### 数据区详解

方法区：存放static，final，Class，常量池

记录者即PC寄存器：指向方法区中的方法字节码

>1. 加载Main类，把类、方法，final等静态放入方法区
>2. 由于需要new，因此将引用放入栈
>3. 在堆中生成数据，如果赋值则直接通过方法区进行赋值
>4. 通过栈进行引用堆中地址

```java
package chapter1;
public class Demo {
    public static void main(String[] args) {
        Pet pet = new Pet();
        pet.name="旺财";
        pet.age =13;
        pet.shout();
    }
    //native:说明调用底层C语言的库
    //本地方法栈native method  调用本地接口JNI，使用本地方法库，扩展java使用，融合

}
class Pet{
    public String name;
    public int age;
    public void shout(){
        System.out.println("shout");
    }
}
```

![image-20200520114715941](https://gitee.com/shaobing2021/typora/raw/master/img/20200728100352.png)



#### 栈

栈：栈内存，主管程序的运行，生命周期和线程同步

线程结束：栈内存就是释放，对于栈来说，不存在垃圾回收问题，一旦线程结束，栈就Over

栈里面存：8大数据类型+对象引用+实例方法

栈运行原理：栈帧

#### 值传递和引用传递

https://www.zhihu.com/question/31203609

```
package chapter1;

public class Test {
    public static void main(String[] args) {
        int[] a= {1,3};
        int[] b = a;
        b[1]= 5;
        System.out.println(a[1]);//5
    }
}
```

#### 堆

![image-20200520142620966](https://gitee.com/shaobing2021/typora/raw/master/img/20200728100353.png)

Heap，一个JVM只有一个堆内存

* 元空间逻辑上存在，物理并不存在：因为新生区+老年代空间空间=堆空间

* 新生区：轻GC，分为：伊甸区eden space区，幸存区，幸存区

1. 类诞生和成长的地方，甚至死亡
2. 伊甸园，所有对象都是在伊甸园区new出来的
3. 幸存者（分成0，1）

* 养老区：重GC
* 永久存储区:jdg1.8之后改为元空间metaspace

永久存储存放JDK自身携带的Class对象。Interface元数据，存储的是Java运行时的一些环境

永久区不存在垃圾回收，关闭vm虚拟就会释放这个区域的内存~

永久区崩溃:一个启动类加载了大量的jar包，Tomcat部署了太多的应用，大量动态生成的反射类，不断地被加载。直到内存满就会出现OOM

1. jdk1.6之前：永久代，常量池在方法区
2. jdk1.7之前：永久代，常量池在堆中
3. jdk1.8之后：无永久代，常量池在元空间

假设内存满了：OOM:java.lang.OutOfMemoryError:

真理：99%的对象都是临时对象

#### OOM问题

* 扩大堆内存看结果
* 分析内存，看哪里出问题

##### 产生OOM：

```
//配置VM   初始化内存分配  最大内存分配  GC垃圾回收信息
//-XX:+HeapDumpOnOutofMemoryError
//options :-Xms2m -Xmx2m -XX:+PrintGCDetails
package chapter1;
public class Hello {
    public static void main(String[] args) {
        String str = "狂";
        while (true){
            str+=str+999999999;
        }
    }
}
```

部分错误信息

```
[0.006s][warning][gc] -XX:+PrintGCDetails is deprecated. Will use -Xlog:gc* instead.
[0.020s][info   ][gc,heap] Heap region size: 1M
[0.021s][info   ][gc     ] Using G1
[0.021s][info   ][gc,heap,coops] Heap address: 0x00000000ffe00000, size: 2 MB, Compressed Oops mode: 32-bit
[0.116s][info   ][gc,start     ] GC(0) Pause Young (Normal) (G1 Evacuation Pause)
[0.117s][info   ][gc,task      ] GC(0) Using 2 workers of 4 for evacuation
[0.118s][info   ][gc,phases    ] GC(0)   Pre Evacuate Collection Set: 0.0ms
[0.118s][info   ][gc,phases    ] GC(0)   Evacuate Collection Set: 1.6ms
[0.118s][info   ][gc,phases    ] GC(0)   Post Evacuate Collection Set: 0.1ms
[0.118s][info   ][gc,phases    ] GC(0)   Other: 0.2ms
[0.118s][info   ][gc,heap      ] GC(0) Eden regions: 1->0(1)
[0.118s][info   ][gc,heap      ] GC(0) Survivor regions: 0->1(1)
[0.118s][info   ][gc,heap      ] GC(0) Old regions: 0->0
[0.118s][info   ][gc,heap      ] GC(0) Humongous regions: 0->0
[0.118s][info   ][gc,metaspace ] GC(0) Metaspace: 334K->334K(1056768K)
[0.118s][info   ][gc           ] GC(0) Pause Young (Normal) (G1 Evacuation Pause) 1M->0M(2M) 2.092ms
[0.119s][info   ][gc,cpu       ] GC(0) User=0.02s Sys=0.00s Real=0.00s
[0.227s][info   ][gc,start     ] GC(1) Pause Young (Normal) (G1 Evacuation Pause)
[0.227s][info   ][gc,task      ] GC(1) Using 2 workers of 4 for evacuation
[0.232s][info   ][gc           ] GC(1) To-space exhausted
[0.232s][info   ][gc,phases    ] GC(1)   Pre Evacuate Collection Set: 0.0ms
[0.232s][info   ][gc,phases    ] GC(1)   Evacuate Collection Set: 2.6ms
[0.232s][info   ][gc,phases    ] GC(1)   Post Evacuate Collection Set: 1.8ms
[0.232s][info   ][gc,phases    ] GC(1)   Other: 0.1ms
[0.232s][info   ][gc,heap      ] GC(1) Eden regions: 1->0(1)
[0.232s][info   ][gc,heap      ] GC(1) Survivor regions: 1->0(1)
[0.232s][info   ][gc,heap      ] GC(1) Old regions: 0->2
[0.232s][info   ][gc,heap      ] GC(1) Humongous regions: 0->0
[0.232s][info   ][gc,metaspace ] GC(1) Metaspace: 877K->877K(1056768K)
[0.232s][info   ][gc           ] GC(1) Pause Young (Normal) (G1 Evacuation Pause) 1M->1M(2M) 4.610ms
[0.232s][info   ][gc,cpu       ] GC(1) User=0.00s Sys=0.00s Real=0.00s
[0.232s][info   ][gc,task      ] GC(2) Using 1 workers of 4 for full compaction
[0.232s][info   ][gc,start     ] GC(2) Pause Full (G1 Evacuation Pause)
```

##### 排错

安装plugins插件：jpro

安装jpro客户端：jprofiler，百度下载

#### GC常用算法

JVM在进行GC时，并不是堆这三个区域同时回收，大部分时候回收是新生代

幸存区（0，1）也是（from,to）两个相互交换

GC题目

* JVM内存模型和分区:老年代占2/3，新生代1/3：Eden占8/10，幸存区各1/10
* 堆里面的分区有哪些：eden，from，to，old

> GC算法

* 标记清除法:对活着的对象进行标记，对没有标记的对象清除

1. 优点：不需要额外空间
2. 缺点：两次扫描，严重浪费时间，会产生内存碎片，需要哈希查找空位

* 标记整理法（标记压缩）又叫标记清除压缩算法 

1. 压缩：防止内存碎片，再次扫描，将存活对象前移

* 引用技术器（为每个对象使用计数，不使用），
* 复制算法（主要使用）

1. 每次GC都会将Eden活的对象移到幸存区：一旦Eden区被GC就会是空的
2. 谁空谁是to，合并两个幸存者，谁多谁是爹，少的是儿子，儿子移动到爹里面，from移动到to
3. 当一个对象经历默认十五次GC都还没有死，-XX:MaxTenuringThreshold  设定进入老年代时间
4. 好处：没有内存碎片，坏处：浪费了内存空间，永远有一半空间是空的。极端情况：对象100%存活，则会开销极大，from几乎全部移动到to。因此最佳使用场景：对象存活度极低：新生区

* 轻GC和重GC分别在什么时候发生

Minor GC触发条件：当Eden区满时，触发Minor GC。

Full GC触发条件：

（1）调用System.gc时，系统建议执行Full GC，但是不必然执行

（2）老年代空间不足

（3）方法去空间不足

（4）通过Minor GC后进入老年代的平均大小大于老年代的可用内存

（5）由Eden区、From Space区向To Space区复制时，对

#### GC总结

内存效率（时间复杂度）：复制算法（1）>标记清除算法（2）>标记压缩算法（3）

内存整齐度：复制算法=标记压缩算法>标记清除算法

内存利用率：标记压缩算法=标记清除算法>复制算法

没有最优算法，GC:分代收集算法

年轻代：

* 存活率低
* 复制算法

老年代：

* 区域大，存活率高
* 标记清除（内存碎片不是太多）+标记压缩算法

三种JVM

查看本地虚拟机类型：java -version

#### JMM

https://www.cnblogs.com/yuxiang1/archive/2019/05/16/10875619.html

JAVA Memory Model：Java内存模型

* Java 内存模型（JMM）是一种抽象的概念，并不真实存在，它描述了一组规则或规范，通过这组规范定义了程序中各个变量（包括实例字段、静态字段和构成数组对象的元素）的访问方式。试图屏蔽各种硬件和操作系统的内存访问差异，以实现让 Java 程序在各种平台下都能达到一致的内存访问效果。

注意JMM与JVM内存区域划分的区别：

- JMM描述的是一组规则，围绕原子性、有序性和可见性展开；
- 相似点：存在共享区域和私有区域

主内存与工作内存

处理器上的寄存器的读写的速度比内存快几个数量级，为了解决这种速度矛盾，在它们之间加入了高速缓存。

加入高速缓存带来了一个新的问题：缓存一致性。如果多个缓存共享同一块主内存区域，那么多个缓存的数据可能会不一致，需要一些协议来解决这个问题。

volatile

八种指令

