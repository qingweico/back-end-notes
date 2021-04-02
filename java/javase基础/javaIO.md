# 目录

[TOC]

## IO

![未命名文件](https://pic.downk.cc/item/5f7ea8f91cd1bbb86ba8c15c.png)

![CharStream](https://pic.downk.cc/item/5ef3034f14195aa594c4649d.png)

### 常用的流

流根据方向可以分为输入流和输出流（相对内存而言）,输入流也叫Read(读),输出流也叫Writer(写)

流根据类型分为字节流和字符流,前者一次读取一个字节,后者一次读取两个字节

- FileInputStream
- FileOutputStream
- FileReader
- FileWriter



- BufferedInputStream
- BufferedOutputStream

- BufferedReader
- BufferedWriter



- InputStreamReader
- OutputStreamWriter



- ObjectInputStream
- ObjectOutputStream
- DataInputStream
- DataOutputStream



- PrintStream
- PrintWriter

### FileInputStream

文件字节输入流  将计算机硬盘中的内容写到内存中

#### int read()

文件temp.txt的内容为abcdefg   注意不要换行

```java
package io;
import java.io.FileInputStream;
import java.io.IOException;
public class ByteStream {
    public static void main(String[] args) {
        FileInputStream fis = null;
        try {
           String filePath = "src/io/temp.txt";//可以是相对路径,也可以是绝对路径
           fis = new FileInputStream(filePath);
           int temp;
           while ((temp = fis.read()) != -1){
               System.out.println(temp); 
           }
        } catch (IOException e){
               e.printStackTrace();
           } finally {
               try {
                   if(fis != null){
                       fis.close();
                   }
               }catch (IOException e){
                   e.printStackTrace();
               }
       }
    }
}
```

#### int read(byte[] bytes)

读取数据之前在内存中准备一个字节数组,一次读取多个字节存入字节数组中,效率高。返回每次读取到数据的字节长度

```java
package io;
import java.io.FileInputStream;
import java.io.IOException;
public class ByteStream {
    public static void main(String[] args) throws IOException {
        String filePath = "src/io/temp.txt";
        FileInputStream fis = new FileInputStream(filePath);
        byte[] bytes = new byte[3];
        int a = fis.read(bytes);
        System.out.println(a);
        System.out.println(new String(bytes));
        int b = fis.read(bytes);
        System.out.println(b);
        System.out.println(new String(bytes));
        int c = fis.read(bytes);
        System.out.println(c);
        System.out.println(new String(bytes));
        int d = fis.read(bytes);
        System.out.println(d);
        System.out.println(new String(bytes));
    }
}
```

*![image-20200607142356003](https://pic.downk.cc/item/5ef3034214195aa594c461ab.png)*

由于每次读到内存中的byte数组中的数据不会刷新而会覆盖,当读到最后的数据不足字节数组的长度时则会出现数据重复,因而可以使用方法new String(byte[] bytes,int offset,int length);来获取每次读到的有效数据长度

#### new String(byte[] bytes,int offset)

```java
package io;
import java.io.FileInputStream;
import java.io.IOException;
public class ByteStream {
    public static void main(String[] args) throws IOException {
        String filePath = "src/io/temp.txt";
        FileInputStream fis = new FileInputStream(filePath);
        byte[] bytes = new byte[3];
        int temp;
        while ((temp = fis.read(bytes)) != -1){
            System.out.println(new String(bytes,0,temp));
        }
    }
}
```

*![image-20200607143429406](https://pic.downk.cc/item/5ef3034214195aa594c461af.png)*

### FileOutputStream

文件字节输出流 将内存中的数据写入计算机硬盘中

#### void write(byte[] bytes)

```java
package io;
import java.io.FileOutputStream;
import java.io.IOException;
public class ByteStream {
    public static void main(String[] args) throws IOException {
        String filePath = "src/io/temp";       //文件不存在则自动创建
        FileOutputStream fos = new FileOutputStream(filePath,true);//true表示追加写,默认为false覆盖写
        String s = "Hello World";
        byte[] bytes = s.getBytes();
        fos.write(bytes);
        fos.close();
    }
}
```

#### void write(byte[] bytes,int offset,int length)

将字节数组的一部分写入

### 使用字节流完成文件的复制

```java
package io;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;
public class ByteStream {
    public static void main(String[] args) throws IOException {
        String filePath = "src/io/temp";
        FileInputStream fis = new FileInputStream(filePath);
        FileOutputStream fos = new FileOutputStream("src/io/temp.txt",true);
        byte[] bytes = new byte[1024];
        int temp;
        while((temp = fis.read(bytes)) != -1){
            fos.write(bytes,0,temp);
        }
        fos.close();
        fis.close();
    }
}
```

### FileReader

文件字符输入流

```java
package io;
import java.io.FileReader;
import java.io.IOException;
public class CharStream {
    public static void main(String[] args) throws IOException {
        String filePath = "src/io/temp";
        FileReader fr = new FileReader(filePath);
        char[] ch = new char[512];
        int temp;
        while((temp =fr.read(ch)) != -1){
            System.out.println(new String(ch,0,temp));
        }
    }
}
```

### FileWriter

文件字符输出流

```java
package io;
import java.io.FileWriter;
import java.io.IOException;
public class CharStream {
    public static void main(String[] args) throws IOException {
        String filePath = "src/io/temp";
        FileWriter fw = new FileWriter(filePath,true);
        String s = "hello";
        char[] ch = {'h','e','l','l','o'};
        fw.write(s);          //直接写入字符串
        fw.write(s,0,2);      //写入部分字符串
        fw.write(ch);         //写入字符数组
        fw.write(ch,0,3);     //写入部分字符数组
        fw.close();
    }
}
```

### 使用字符流完成文件的复制

```java
package io;
import java.io.File;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;
public class CharStream {
    public static void main(String[] args) throws IOException {
        FileReader fr = new FileReader("src/io/temp");
        FileWriter fw = new FileWriter("src/io/temp.txt",true);
        char[] ch = new char[512];
        int temp;
        while((temp = fr.read(ch)) != -1){
            fw.write(ch,0,temp);
        }
        fw.close();
        fr.close();
    }
}
```

### BufferedInputStream

带有缓冲区的字节输入流

### BufferedOutputStream

带有缓冲区的字节输出流

### BufferedReader

带有缓冲区的字符输入流

```java
package io;
import java.io.*;
public class CharStream {
    public static void main(String[] args) throws IOException {
        BufferedReader br= new  BufferedReader(new FileReader("src/io/temp"));
        String temp;
        while((temp = br.readLine()) != null){  //每次读取一行
            System.out.println(temp);
        }
    }
}
```

```java
package io;
import java.io.*;
public class CharStream {
    public static void main(String[] args) throws IOException {
        BufferedReader br= new  BufferedReader(new FileReader("src/io/temp"));
        char[] ch = new char[512];
        int temp;
        while ((temp = br.read(ch)) != -1){   //每次读取一个k
            System.out.println(new String(ch,0,temp));
        }
    }
}
```

```java
package io;
import java.io.*;
public class CharStream {
    public static void main(String[] args) throws IOException {
        BufferedReader br= new  BufferedReader(new FileReader("src/io/temp"));
        char[] ch = new char[512];
        int temp;
        while ((temp = br.read(ch,0,1)) != -1){     //每次只会读取一个字符长度的数据存到数组中
            System.out.println(new String(ch,0,temp));
        }
    }
}
```

### BufferedWriter

带有缓冲区的字符输出流

```java
package io;
import java.io.*;
public class CharStream {
    public static void main(String[] args) throws IOException {
      BufferedWriter bw= new BufferedWriter(new FileWriter("src/io/temp",true));
      bw.write("hello");
      bw.newLine();         
      bw.close();
    }
}
```

BufferedWriter和FileWriter使用方法差不多,前者可以换行

### 使用BuffferedReader和BufferedWriter

```java
package io;
import java.io.*;
import java.util.*;
public class Io_ArrayListRandom {
   public static void main(String[] args)  throws IOException {
      BufferedReader br = new BufferedReader(new InputStreamReader(new FileInputStream("src/io/temp"), "GBK"));
      ArrayList<String> arr = new ArrayList<>();
      String s;
      while ((s = br.readLine()) != null) {
         arr.add(s);
      }
      br.close();
      Random r = new Random();
      int index = r.nextInt(arr.size());//产生大小在集合长度范围内的下标
      System.out.println(arr.get(index));
      arr.remove(index);                //移出随机产生下标对应的数据        
      BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(new FileOutputStream("src/io/temp", true), "GBK"));
      for (String name : arr) {         //将剩下的数据写入原来文件中
         bw.write(name);
         bw.newLine();
      }
      bw.close();
   }
}
```

### InputStreamReader

转换流（字节输入流 --> 字符输入流 ）

```java
BufferedReader br = new BufferedReader(new InputStreamReader(new FileInputStream("src/io/temp")));
```

### OutputStreamWriter

转换流（字节输出流 --> 字符输出流 ）

```java
BufferedWriter bw= new BufferedWriter(new OutputStreamWriter(new FileOutputStream("src/io/temp",true)));
```

### PrintStream

标准的输出流,默认以字节的方式打印到控制台

```java
PrintStream ps = System.out;
ps.println("");        ---> System.out.println("");
```

```java
package io;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.PrintStream;
import java.text.SimpleDateFormat;
import java.util.Date;
public class Io_PrintStream {
    public static void main(String[] args) {
        try{
            //System默认打印到控制台
            PrintStream ps = System.out;
            ps.println("java");
             //通过setOut方法改变打印输出方向
            System.setOut(new PrintStream(new FileOutputStream("src//io//log")));
            System.out.println(new SimpleDateFormat("yyyy-MM-dd HH-mm-ss").format(new Date()));
            System.out.println("打印日志");
        }catch (FileNotFoundException e){
            e.printStackTrace();
        }
    }
}
```

### PrintWriter

以字符方式

```java
package io;
import java.io.*;
public class Io_PrintStream {
    public static void main(String[] args) throws IOException {
        PrintWriter pw = new PrintWriter(new FileWriter("src/io/temp",true));
        String[] dest = new String[]{"hello","world"};
        for(String s : dest){
            pw.println(s);      //自动刷新和换行
        }
        pw.close();             //必须关闭
    }
}
```

### boolean endWith()

寻找所有以.dll结尾的文件

```java
package io;
import java.io.File;
public class File_ListFiles {
   public static void main(String[] args) {
      String filePath = "";
      File f = new File(filePath);
      File [] files = f.listFiles();
      for(File file:  files) {
         if(file.isFile()) {
            if(file.getName().endsWith(".dll")) {
               System.out.println(file.getName());
            }
         }
      }
    }
}
```

```java
package io;
import java.io.File;
public class File_ListFiles {
   public static void main(String[] args) {
      String filePath = "";
      File folder = new File(filePath);
      File[] fileArray = folder.listFiles((dir, name) -> new File(dir, name).isFile() && name.endsWith(".dll"));
      assert fileArray != null;
      for (File file : fileArray) {
         System.out.println(file.getName());
      }
   }
}
```

### 递归遍历一个文件夹下所有的文件

```java
package io;
import java.io.File;
public class FileList_Java {
    public static void fileList(File target) {
        if (target.isFile()) {
            return;
        }
        File [] files = target.listFiles();
        for(File file : files){
            System.out.println(file.getName());
            fileList(file);
        }
    }
    public static void main(String[] args) {
        String filePath = "";
        fileList(new File(filePath));
    }
}
```

### 复制文件并更改后缀名

```java
package io;
import java.io.BufferedInputStream;
import java.io.BufferedOutputStream;
import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;
public class Io_ChangeName {
   public static void copy(File file,File newFile)throws IOException {
      BufferedInputStream  fis = new BufferedInputStream(new FileInputStream(file));
      BufferedOutputStream fos = new BufferedOutputStream(new FileOutputStream(newFile,true));
      byte []bs = new byte[1024];
      int len;
      while((len = fis.read(bs)) != -1) {
         fos.write(bs,0,len);
      }
      fos.close();
      fis.close();
   }
   public static void main(String[] args) throws IOException {
       String targetFile = "";
       String destFile = "";
       File target = new File(targetFile);
       File  dest = new File(destFile);
       if(!dest.exists()) ff.mkdir();
       File[] files = target.listFiles();
       assert files != null;
      for(File file : files) {
         String name = file.getName();
         System.out.println(name);
         name = name.replace(".java", ".txt");
         File newFile = new File(dest,name);
         copy(file,newFile);
       }
   }
}
```

### Scanner

```java
package io;
import java.io.File;
import java.io.FileNotFoundException;
import java.util.Scanner;
public class Io_Scanner {
    public static void main(String[] args){
        try{
            File file = new File("src/io/temp");
            Scanner sc = new Scanner(file);
            while (sc.hasNext()) {
                System.out.println(sc.next());
            }
            sc.close();
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        }
    }
}
```

### LineNumberReader

```java
package io;
import java.io.FileReader;
import java.io.IOException;
import java.io.LineNumberReader;
public class Io_LineNumberReader {
   public static void main(String[] args) throws IOException {
     LineNumberReader  lnr = new LineNumberReader(new FileReader("src/io/temp"));
     String s;
     lnr.setLineNumber(100);              //设置起当前行数
     while((s = lnr.readLine()) != null) {
       System.out.println(lnr.getLineNumber()+":"+s);
     }
     lnr.close();
    }
}
```

### ByteArrayOutputStream

内存操作流：数据在内存中转一圈

```java
package io;
import java.io.ByteArrayInputStream;
import java.io.ByteArrayOutputStream;
import java.io.IOException;
public class Io_ByteArray {
   public static void main(String[] args) throws IOException {
   ByteArrayOutputStream baos = new ByteArrayOutputStream();
   baos.write("hello".getBytes());
   byte[] bs = baos.toByteArray();
   ByteArrayInputStream  bais = new ByteArrayInputStream(bs);
   int len;
     while((len = bais.read()) != -1){
       System.out.print((char) len);
    }
   }
}
```

- 磁盘操作：File
- 字节操作：InputStream 和 OutputStream
- 字符操作：Reader 和 Writer
- 对象操作：Serializable
- 网络操作：Socket
- 新的输入/输出：NIO

复制图片,视频,音频等：只能用字节流,而不能使用字符流

复制纯文本可以使用字符流.

用字节流向文件中写入数据时,必须要转换为字节类型或用字节数组,比如字符串是直接写不进去的

而用字符流时用字符串和字符都行

字节流在使用后没有关闭写数据管道时仍然可以成功写入数据（带有缓冲区的除外）

字符流在使用后没有关闭写数据管道时,此时写入数据失败,系统就会认为还有数据一直在等待中,使用flush方法强行写入

### 通过键盘输入的两种方法

 1:通过Scanner方法

```java
 Scanner sc = new Scanner(System.in);//System.in是一个标准的输入流 默认接受键盘的输入
```

  2:通过BufferedReader

```java
BufferedReader input = new BufferedReader(new InputStreamReader(System.in));
String s = input.readLine();
input.close();
```

