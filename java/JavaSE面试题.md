# 目录

[TOC]

## J2SE面试题

### equal 和 `==`

equal只比较变量的内容

==会比较变量的内容和地址

```java
public class Test{
    public static void main(String[] args)  {
       String a = "hello";
       String b = "hello";
       System.out.println(a == b);  //true 直接赋值字符串时会把字符串储存在字符串常量池中，当再次赋值时会到常量池中寻找，找到会指向堆中的地址，找不到会在堆中重新开辟一块空间
       System.out.println(a.equals(b)); //true
   }
}
```

*![1589949211111](https://pic.downk.cc/item/5ef2f94214195aa594c2a1c4.png)*

```java
public class Test{
public static void main(String[] args)  {
    String c = new String("hello");
    String d = new String("hello");
    System.out.println(c == d);  //false  使用new关键字会直接在堆空间中开辟一块新的空间
    System.out.println(c.equals(d)); //true
   }
}
```

*![1589949596424](https://pic.downk.cc/item/5ef2f94214195aa594c2a1c6.png)*

```java
public class String_Java {
    public static void main(String[] args){   
        String s = "hello";                   
        String s1 = "he";
        String s2 = "llo";
        String s3 = "he"+"llo";      //"he" 和"llo"都为字符串常量，在预编译时期“+”被优化
        System.out.println(s == s3); //true 相当于直接把两个字符串常量自动合成为一个字符串常量
        System.out.println(s1 == (s2 + s1));//false
        //因为字符串+操作就是在程序运行时new了StringBuilder对象
        //然后调用append()方法，拼接完成后再调用toString()方法返回一个String对象
    }
}
```

- 字符串和字符串常量是两个不同的概念

- 字符串常量是储存在本地方法区，而字符串则储存在堆里(heap)

### lambad表达式

```java
//遍历list
public class Test{
    public static void main(String[] args)  {
        List<String> strings = Arrays.asList("hello", "world", "java");
        strings.forEach((string)-> System.out.println(string));
        // strings.forEach(System.out::println);
    }
}
```

```java
//遍历map集合
public class Test{
    public static void main(String[] args)  {
       Map<String,Object> maps = new HashMap<>();
       maps.put("1","hello");
       maps.put("2","world");
       maps.put("3","java");
       maps.forEach((k,v)-> System.out.println(k+"--"+v));
}
```

### 包装类

```java
public class Test{
    public static void main(String[] args) {
       Integer a = 127,b = 127,c = 128,d = 128;
        System.out.println(a == b);  //true  
        System.out.println(c == d);  //false
    }
}
```

int包装类Integer的缓存区只有一个字节大小（-128~127），超过一个字节会重新开辟一块空间

### 跳出多重循环

```java
public class Test{
    public static void main(String[] args) {
        flag:
       for(int i = 1;i < 10;i++){
           for(int j = 0;j < 10;j++){
               System.out.println(j);
               break flag;
           }
       }
    }
}
```

### native修饰符

native代表java访问其他语言遍写的代码

*![1589950395886](https://pic.downk.cc/item/5ef2f94214195aa594c2a1c8.png)*

### &和&&的区别

&& ：逻辑与 ，短路与  当第一个条件为false时，第二个条件不会再执行

& ：无论第一个条件是否为false，都会执行第二个条件

```java
public class Test{
    public static void main(String[] args) {
        int a = 2;
        int b = 3;
        boolean flag = (++a > 3)&&(++b > 4);
        System.out.println(a);  //3
        System.out.println(b);   //4
        System.out.println(flag); //false
    }
}
```

### 显示（强制）类型转换和隐式类型转换

八种数据类型之间除了boolean之外的其中类型都可以相互转换

任何浮点类型不管占用多大的字节都要比整型容量大

char和short虽然所占的字节数一样，因为char类型没有负数，可以取得比short更大的整数

当byte,char,short没有超过各自的字面值大小时可以直接赋值

当byte,char,short混合运算时都会转换为int类型再做运算

多种数据类型混合运算时，先转换为最大的那种类型再做运算

#### 强制转换

将大容量的数据类型转换为小容量的数据类型称为强制转换，会发生精度丢失

```java
public class Test{
    public static void main(String[] args) {
        byte b = (byte) 128;              //128默认为int型 0000 0000 1000 0000
        System.out.println(b); //-128     //强制转化为byte型为 1000 0000(补码)
     }                                    //原码还是1000 0000即-128
}                                         //1000 0000（-128）
```

```java
int a = -129;     
System.out.println((byte)a); //127
/*-129的原码是1000 0000 0000 0000 0000 0000 1000 0001
在计算机中保存的是补码所以是
             1111 1111 1111 1111 1111 1111 0111 1111
强制转换为byte即0111 1111(补码) 因为0111 1111一看最高位是0所以为正数即为127
*/
```

正数的反码和补码是其本身

负数的反码是除符号位全部取反，补码为反码加一

在计算机系统中，数值一律采用补码的形式储存

计算(byte)数值的原码：8  直接计算 0000 1000

计算负数的原码：

- 先计算补码    该负数对应的正数的原码所有位都取反包括最高位，再加一，得到该负数补码
- 然后补码减一，除了最高位全部取反即可

```java
public class Test{
    public static void main(String[] args) {
        byte a = -68;
        if(a < 0){;
           a += 256;           /*byte类型的256为0 等价于    a = (byte) (a  + 256) +=操作符 会进行自动强制转换
                               byte与int混合运算时byte类型的a会转换为int类型的不会发生精度丢失即还是-68加上256即为                                  188，此时188为int类型 原码为00000000 00000000 00000000 1011 1100
                               强转为byte类型为1011 1100(补码)
                                             1011 1011(反码)
                                             1100 0100(原码) 即为-68*/
        }
        System.out.println(a);  //-68
    }
}
```

####  隐式转换

小容量的数据类型转换为大容量的数据类型称为自动类型转换也叫隐式转换

byte < short(char) < int < long < float < double

### 静态变量，静态方法和静态代码块以及构造方法的执行顺序

静态变量最先执行，然后执行静态代码块，接着执行静态方法，最后执行构造方法

### StringBuffer和StringBuilder的区别

***StringBuffer线程安全 但是执行速度慢(相对于StringBuilder而言)***
***StringBuilder线程不安全 但是执行速度快(相对于StringBuffer而言)***    

##### 执行效率StringBuilder>StringBuffer>String  

***单线程操作字符串缓冲区下操作大量数据使用StringBuilder*** 

***多线程下字符串缓冲区下操作大量数据使用StringBuffer***

***StringBuffer内部被synchronized关键字修饰，执行效率会慢于StringBuilder***

### 常见的加密算法有哪些？并说明一种

```java
常见的加密算法有DES AES Ras Base64 MD5
```

#### MD5加密

```java
import java.nio.charset.StandardCharsets;
import java.security.MessageDigest;
import java.security.NoSuchAlgorithmException;
import java.util.Scanner;
public class Test{
    public static void main(String[] args) throws NoSuchAlgorithmException{
       MessageDigest md5 = MessageDigest.getInstance("MD5");
       String[] content = {"0","1","2","3","4","5","6","7","8","9","a","b","c","d","e","f"};
        Scanner input = new Scanner(System.in);
        String plainText = input.nextLine();
        StringBuilder cipherText = new StringBuilder();
        byte[] digest = md5.digest(plainText.getBytes(StandardCharsets.UTF_8));
        for(byte temps : digest){
           int temp = temps;
            if(temp < 0){
                temp += 256;
            }
            int firstIndex = temp/16;
            int secondIndex = temp%16;
            cipherText.append(content[firstIndex]).append(content[secondIndex]);
        }
System.out.println("明文" + plainText + "加密之后的密文是" + cipherText);
```

### java内存结构

- 栈内存：用于储存局部变量，数据使用完后，空间会自动释放

- 堆内存：用来储存引用类型变量的内容

- 方法区：静态成员，构造函数，常量池和线程池

- 本地方法区：系统占用

