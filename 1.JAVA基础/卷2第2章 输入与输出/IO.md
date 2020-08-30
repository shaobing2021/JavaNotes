## IO

#### IO简介

IO流分类：

* 按照方向分类：输入流、输出流
* 按照读取数据方式
  1. 字节流：每次读取1个字节byte，等同于每次读取8个二进制位，可以读取任何文件**java.io.inputstream/outputstream**
  2. 字符流：一次读取一个字符。只能读取普通文本文件，不能读取word                       **java.io.reader/writer**
* 所有流均实现java.io.closeable接口，可关闭
* 所有**输出流**均可刷新，都有flush()方法，将管道数据输出（好像jdk1.8之后关闭之前均会刷新？不刷新可能导致丢失数据）
* 需要掌握16个流：
  1. 文件专属：java.io.FileinputStream、FileoutputStream、java.io.FileReader、java.io.FileWriter
  2. 转换流:将字节流转换成字符流: java.io.inputstreamreader,java.io.outputstreamwriter
  3. 缓冲流：java.io.bufferedreader,java.io.bufferedwriter,bufferedinputstream,bufferedoutputstream
  4. 数据流专属: java.io.datainputstream,java.io.dataoutputstream
  5. 标准输出流：java.io.printwriter,java.io.printstream
  6. 对象专属流：java.io.objectInputstream,java.io.objectoutputstream

![image-20200506144728251](https://gitee.com/shaobing2021/typora/raw/master/img/20200728094128.png)

![image-20200506145019558](https://gitee.com/shaobing2021/typora/raw/master/img/20200728094129.png)

### 1.FileinputStream

##### 绝对路径读取

一次读取一个字符（英文字符一个字节，中文字符二个或三个字节）即8个二进制效率过低，

```
package IO;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
public class FileInputStreamTest1 {
    public static void main(String[] args) {
        FileInputStream fis = null;
        try {
             fis = new FileInputStream("F:\\java\\IO\\IO笔记\\1.txt");
            //返回读取的字节“97（a）”,读取到最后返回-1
            int reddData = 0;
            while ((reddData = fis.read())!=-1){
                System.out.println(reddData);
            }
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (fis!=null){
                try {
                    fis.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }

        }
    }
}
```

##### 相对路径

###### 1.获取当前路径

在IDEA中默认的当前路径是project的跟。

读取根目录下文件方式

```java
package IO;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
public class FileInputStreamTest1 {
    public static void main(String[] args) {
        FileInputStream fis = null;
        try {
            System.out.println(System.getProperty("user.dir"));
//             fis = new FileInputStream("F:\\java\\IO\\IO笔记\\1.txt");
            //返回读取的字节“97（a）”,读取到最后返回-1
           fis = new FileInputStream("module/src/chapter1/Hello.java");
            int reddData = 0;
            while ((reddData = fis.read())!=-1){
                System.out.println(reddData);
            }
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (fis!=null){
                try {
                    fis.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}

```

###### 2.byte读取

```
//假设abcdef六个字节,每次读取4个字节
byte[] bytes = new byte[4];
//本次读取4个字节，abcd
int readcount= fis.read(bytes);
//继续读则读取两个字节，此时内存里面数据为：efcd
 readcount= fis.read(bytes);
 //读取完毕则返回-1
 //因此不能将字节数组全部转化成字符串
 new String（bytes，0，readcount）
```

###### 3.读取终版

```java
package IO;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
public class FileInputStreamTest1 {
    public static void main(String[] args) {
        FileInputStream fis = null;
        try {
            fis = new FileInputStream("F:\\java\\IO\\IO笔记\\1.txt");
//           fis = new FileInputStream("module/src/chapter1/Hello.java");
            byte[] bytes = new  byte[10];
            int data = 0;
            while (( data= fis.read(bytes))!=-1){
                System.out.println(bytes);
                System.out.println(new String(bytes,0,data));
            }
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (fis!=null){
                try {
                    fis.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}

```

###### 4.其他方法

```
//剩余多少个
fis.available()
//跳过多少个字节
fis.skip()
```

### 2.FileReader

##### 1.路径问题

![image-20200503224033163](https://gitee.com/shaobing2021/typora/raw/master/img/20200728094130.png)

可以从图中看出test路径和main方法路径不同

##### 2.读取文件

```java
package IO;
import org.junit.Test;
import java.io.File;
import java.io.FileNotFoundException;
import java.io.FileReader;
import java.io.IOException;

public class FileReaderTest {
    public static void main(String[] args) {
        System.out.println(System.getProperty("user.dir"));
        File file = new File("module\\src\\1.txt");
        FileReader fr = null;
        try {
            fr = new FileReader(file);
            int data = 0;
            while ((data=fr.read())!=-1){
                System.out.println((char)data);
            }
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            if (fr!=null){
                try {
                    fr.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
    @Test
    public void testFileReader(){      System.out.println(System.getProperty("user.dir"));
        File file = new File("1.txt");
        System.out.println(file.getAbsoluteFile());
    }
}
```

##### 3.解释部分疑问

上述定义的data就是读取的单个字符转化而来的asic码值

如果定义了chars，则data是读取的字符长度[0,chars.length]

### 3.FileWriter

#### 1.Filewriter要素

* File是否存在，如果不存在会自动创建
* 如果流使用的构造器是FileWriter（file,false）,则会实现对原有文件的覆盖
* 如果流使用的构造器是FileWriter（file,true）,则会实现对原有文件的增加

#### 2.File读写

```java
@Test
    public void testFileReader(){
        System.out.println(System.getProperty("user.dir"));
        //1.File类的实例化
        File read = new File("src\\1.txt");
        File write = new File("src\\2.txt");
        //2.流的实例化
        FileReader fr = null;
        FileWriter fw = null;
        try {
            fr = new FileReader(read);
            fw = new FileWriter(write);
            char[] chars = new char[4];
            int data ;
            while ((data = fr.read(chars))!=-1){
                System.out.println(new String(chars,0,data));
                fw.write(chars);
//                System.out.print(chars);
            }
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            //4.流资源的关闭
            if (fr!=null){
                try {
                    fr.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (fw!=null){
                try {
                    fw.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
```

#### 3.FileReader缺陷

无法读取图片，图片是一个一个字节组成

### 4.File流测试

#### 1.封装流

```java
package IO;

import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;

public class FileInputImage {
    public static void main(String[] args) {
        copyFile("F:\\java\\IO\\IO笔记\\1.jpg","F:\\java\\IO\\IO笔记\\2.jpg");
    }
    public static void copyFile(String fileSrc,String fileDest){
        FileInputStream fis = null;
        FileOutputStream fos = null;
        try {
            File src = new File(fileSrc);
            File dest = new File(fileDest);
            fis = new FileInputStream(src);
            fos = new FileOutputStream(dest);
            byte[] bytes = new byte[20];
            int data;
            while ((data = fis.read(bytes))!=-1){
                fos.write(bytes,0,data);
            }

        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (fis!=null){
                try {
                    fis.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if(fos!=null){
                try {
                    fos.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

#### 2.读取结论

* 对于文本文件：建议使用字符流，其实也可以使用字节流，但是不要在控制台输出就没关系
* 对于非文本文件：建议使用字节流，

### 5.缓冲流

#### 1.核心：

属于处理流之一。关闭外层流，不必再关闭内层流

BufferInput测试

```java
package IO;

import java.io.*;

public class FileInputImage {
    public static void main(String[] args) {
        copyFile("F:\\java\\IO\\IO笔记\\1.jpg","F:\\java\\IO\\IO笔记\\2.jpg");
    }
    public static void copyFile(String fileSrc,String fileDest){
        FileInputStream fis = null;
        FileOutputStream fos = null;
        BufferedInputStream bis = null;
        BufferedOutputStream bos = null;
        try {
            File src = new File(fileSrc);
            File dest = new File(fileDest);
            fis = new FileInputStream(src);
            fos = new FileOutputStream(dest);
            bis = new BufferedInputStream(fis);
            bos = new BufferedOutputStream(fos);
            byte[] bytes = new byte[20];
            int data;
            while ((data = bis.read(bytes))!=-1){
                bos.write(bytes,0,data);
            }
            //资源关闭，从外到内
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (bos!=null){
                try {
                    bos.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (bis!=null){
                try {
                    bis.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            //说明：关闭外层流，内层六也会自动关闭
//            if (fis!=null){
//                try {
//                    fis.close();
//                } catch (IOException e) {
//                    e.printStackTrace();
//                }
//            }
//            if(fos!=null){
//                try {
//                    fos.close();
//                } catch (IOException e) {
//                    e.printStackTrace();
//                }
//            }
        }
    }
}

```

#### 2.fiush

由于write有手动flushbuffer，因此不必手动flush

#### 3.缓存原理

* 通过将字节缓冲到内存然后到磁盘比直接通过程序输出到磁盘快
* 缓冲就是通过缓存流操作字符读或者写入内存
* 在内存读取数据比再磁盘读取数据要快
* 缓冲流相当于盆子，把水接满再导入

#### 4.BufferedReader

##### 1.方式一

```java
public static void copytxt(String fileSrc,String fileDest) {
        BufferedReader br = null;
        BufferedWriter bw = null;
        try {
            br = new BufferedReader(new FileReader(fileSrc));
            bw = new BufferedWriter(new FileWriter(fileDest));
            char[] buffer = new char[5];
            int len;
            while ((len = br.read(buffer))!=-1){
                bw.write(buffer,0,len);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            if (br!=null){
                try {
                    br.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (bw!=null){
                try {
                    bw.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
```

##### 2.方式二

bufferedReader有读一行的

```java
String data;
while((data = br.readline())!=null){
	bw.write(data+"\n");//不包含换行符
	//bw.newLine();提供换行操作
}
```

#### 5.文件加密

```
    public static void jiami(String src, String dest){
        FileInputStream fis = null;
        FileOutputStream fos = null;
        try {
            fis = new FileInputStream(src);
            fos = new FileOutputStream(dest);
            byte[] buffer = new byte[4];
            int len;
            while ((len=fis.read(buffer))!=-1){
                /**
                 * buffer输出为指针，其为几个as克码值的
                 * buffer[i]为asii码值
                 * buffer.length一直为4
                 * char(buffer[i])转化成字符，中文则乱码
                 */
                for (int i = 0; i < len; i++) {
                    buffer[i] =  (byte) (buffer[i]^5);
                }
                fos.write(buffer,0,len);
            }

        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            if (fis!=null){
                try {
                    fis.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (fos!=null){
                try {
                    fos.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
```

### 6.转换流

#### 1.读入

* 转化流属于字符流

* InputStreamReader:将一个字节的输入流转化成字符的输入流

* OutputStreamWriter：将一个字符的输出流转化成字节的输入流

* 解码：字节、字节数组---->看不见的转换成看得见的---->字符数组、字符串

* 编码：将看得见的转化成看不见的     字符转化成字节

  解码操作

```
package IO;

import java.io.FileInputStream;
import java.io.IOException;
import java.io.InputStreamReader;

public class InputStreamReaderTest {
    public static void main(String[] args) {
        test("module\\src\\1.txt");
    }
    public static void test(String src){
        InputStreamReader isr = null;
        try {
            isr = new InputStreamReader(new FileInputStream(src),"utf-8");
            char[] cbuf = new char[5];
            int data;
            while ((data = isr.read(cbuf))!=-1){
                System.out.println(cbuf);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            if (isr!=null){
                try {
                    isr.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}

```

#### 2.读入写出

```
package IO;

import java.io.*;

public class ReaderWriterTest {
    public static void main(String[] args) {
        test("module\\src\\1.txt","module\\src\\3.txt");
    }
    public static  void test(String src, String dest){
        InputStreamReader isr = null;
        OutputStreamWriter osw = null;
        try {
            isr = new InputStreamReader(new FileInputStream(src), "utf-8");
            osw = new OutputStreamWriter(new FileOutputStream(dest), "gbk");
            int len;
            char[] cbuf = new char[5];
            while ((len = isr.read(cbuf))!=-1){
                osw.write(cbuf,0,len);
            }
        }catch (IOException e) {
            e.printStackTrace();
        }finally {
            if (isr !=null){
                try {
                    isr.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (osw!=null){
                try {
                    osw.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }

    }
}

```

#### 3.字符集

常见字符集：

* ASCII:美国标准信息交换码
* ISO8859-1:欧洲码表
* GB2312:中国中文码表、ab是两个字符，连起来可以看成一个字符，因此要看首位。首位是0表示一个字符，首位是1表示两个字节为一个字符
* GBK：上面升级版本
* Unicode：国际标准码：，所有文字都用两个字节表示。缺陷：无法分别 两个Unicode和ASCII，两个字节表示一个字符，而不是一个字符。如果用最高位1，0，表示，则减少很多值无法用与表示字符。

* UTF-8：变长的编码方式，1-4字节

### 7.输入输出流

#### 1.案例

从键盘输入，输入e或者exit则推出

##### 方式一

```
 Scanner scanner = new Scanner(System.in);
        while (true){
            System.out.println("input String:");
            String s = scanner.nextLine();
            if ("e".equalsIgnoreCase(s)){
                System.out.println("exit");
                break;
            }
            System.out.println(s.toUpperCase());
        }
```

##### 方式二

利用bufferedread.readLine方法

```
public static void main(String[] args) {
        InputStreamReader isr = new InputStreamReader(System.in);
        BufferedReader br = new BufferedReader(isr);
        while (true){
            System.out.println("input string:");
            String s = null;
            try {
                s = br.readLine();
            } catch (IOException e) {
                e.printStackTrace();
            }
            if ("e".equalsIgnoreCase(s)||s.equalsIgnoreCase("exit")){
                System.out.println("exit");
                break;
            }
            System.out.println(s.toUpperCase());
        }
        try {
            if (br!=null){
                br.close();
            }

        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    】
```

### 8.打印流数据流

#### 1.打印流

```
 @Test
    public void test2() {
        PrintStream ps = null;
        try {
            FileOutputStream fos = new FileOutputStream(new File("D:\\IO\\text.txt"));
            // 创建打印输出流,设置为自动刷新模式(写入换行符或字节 '\n' 时都会刷新输出缓冲区)
            ps = new PrintStream(fos, true);
            if (ps != null) {// 把标准输出流(控制台输出)改成文件
                System.setOut(ps);
            }


            for (int i = 0; i <= 255; i++) { // 输出ASCII字符
                System.out.print((char) i);
                if (i % 50 == 0) { // 每50个数据一行
                    System.out.println(); // 换行
                }
            }


        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } finally {
            if (ps != null) {
                ps.close();
            }
        }

    }
```

#### 2.数据流

```java
    @Test
    public void test3() throws IOException {
        //1.
        DataOutputStream dos = new DataOutputStream(new FileOutputStream("data.txt"));
        //2.
        dos.writeUTF("刘建辰");
        dos.flush();//刷新操作，将内存中的数据写入文件
        dos.writeInt(23);
        dos.flush();
        dos.writeBoolean(true);
        dos.flush();
        //3.
        dos.close();
    }
    
    @Test
    public void test4() throws IOException {
        //1.
        DataInputStream dis = new DataInputStream(new FileInputStream("data.txt"));
        //2.
        String name = dis.readUTF();
        int age = dis.readInt();
        boolean isMale = dis.readBoolean();

        System.out.println("name = " + name);
        System.out.println("age = " + age);
        System.out.println("isMale = " + isMale);

        //3.
        dis.close();

    }
```

### 9.对象流

用于存储和读取基本数据类型或对象的处理流

序列化：用ObjectOutputStream类保存基本类型数据或对象的机制

反序列化：用ObjectInputStream类读取基本类型数据或对象的机制

无法序列化static和transient修饰的成员变量

```
package chapter1;

import org.junit.Test;

import java.io.*;

public class ObjectInputOutputStreamTest {
    @Test
    public void testOutput(){
        ObjectOutputStream oos = null;
        try {
            oos = new ObjectOutputStream(new FileOutputStream("4.txt"));
            oos.writeObject(new User("我爱北京",11));
            oos.flush();
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            if (oos!=null){
                try {
                    oos.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
    @Test
    public void testInputStream(){
        Object o = null;
        ObjectInputStream ois = null;
        try {
            ois = new ObjectInputStream(new FileInputStream("4.txt"));
            o = ois.readObject();
        } catch (IOException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } finally {
            if (ois==null){
                try {
                    ois.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
        System.out.println(o);
    }
}

```

**transient**不可序列化

### 10.随机存储文件流

属于字节流

* RandomAccessFile继承于java.lang.Object类，实现勒DataInput和DataOutput接口
* 既可以作为一个输入流，也可以作为一个输出流
* r只读方式打开、rw读写方式打开、rwd读写同步内容更新、rws读写打开同步文件内容和元数据的更新

```
package IO;

import org.junit.Test;

import java.io.FileNotFoundException;
import java.io.IOException;
import java.io.RandomAccessFile;

public class RandomAccessFileTest {
    @Test
    public void test1(){
        RandomAccessFile raf = null;
        RandomAccessFile rwf = null;
        try {
            raf = new RandomAccessFile("4.txt","r");
            rwf = new RandomAccessFile("6.txt","rw");
            int len ;
            byte[] buffer = new byte[4];
            while ((len = raf.read(buffer))!=-1){
                rwf.write(buffer,0,len);
            }
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            if (raf!=null){
                try {
                    raf.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (rwf!=null){
                try {
                    rwf.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }

    }
}
```

#### 注意点

* writer默认是实现对文件的覆盖，覆盖部分

#### 文件定位追加

```
@Test
    public void test2(){
        RandomAccessFile raf = null;
        try {
            raf = new RandomAccessFile("4.txt", "rw");
            raf.seek(3);
            StringBuilder sb = new StringBuilder((int) new File("4.txt").length() - 3);
            int len;
            byte[] buffer = new byte[5];
            while ((len = raf.read(buffer))!=-1){
                sb.append(new String(buffer, 0, len));
            }
            raf.seek(3);
            raf.write("xyz".getBytes());
            raf.write(sb.toString().getBytes());
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            if (raf!=null){
                try {
                    raf.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }

    }
```

## NIO

### 概述

NIO支持面向缓冲区，基于通道的IO操作。NIO将以更加高效的方式进行文件的读写操作。

提供两套NIO，一套是针对标准输入输出NIO，另一套是网络编程NIO