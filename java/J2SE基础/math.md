# 目录

[TOC]

### 随机数

```java
Random s=new Random();
s.nextInt(20);  //指定随机数的范围

Math.random();  //产生0到1的随机浮点数,包括0不包括1
```

### 大整数和大浮点数

```java
BigInteger i = new BigInteger("2147483648");                //大整数
```

```java
BigDecimal ssb = new BigDecimal("105445546546546546.2");     //大浮点数
```

### 十进制数转换为二进制

```java
package math;
import java.util.Scanner;
public class Binary_java {
   public static void main(String[] args) {
      //十进制转换为二进制API
      /*String m = Integer.toBinaryString(120);
      System.out.println(m);*/
      Scanner sc = new Scanner(System.in);
      int num = sc.nextInt();
      StringBuilder s = new StringBuilder();
      while(num != 0) {
         s.insert(0,num%2);
         num/=2;
      }
      System.out.println(s);
      sc.close();
   }
}
```

### 二进制转换为十进制

```java
package math;
import java.util.Scanner;
public class Decimal_java {
    //-------------------二进制转换为十进制------------------
    public  static  void main(String[] args){
        Scanner sc = new Scanner(System.in);
        String s = sc.nextLine();
        int num = 0;
        int count = 0;
        for(int j = s.length()-1;j >= 0;j--){
            int i = Integer.parseInt(String.valueOf(s.charAt(j)));
            num += (int)Math.pow(2,count)*i;
            count++;
        }
        sc.close();
        System.out.println(num);
    }
}
```

### 枚举型

```java
package math;
//枚举型
enum Blank {
    java,
    c,
    javascript
}
public class Enum {
    public static void main(String[] args){
        Blank myVar = Blank.javascript;
        System.out.println(myVar);  //javascript
    }
}
```

### 统计元音字母的个数

```java
package math;
import java.util.*;
public class Judge_Vowel {
   public static void main(String[] args) {
   Scanner  sc = new Scanner(System.in);
   String r = sc.nextLine();
   int rr = Integer.parseInt(r);
   for(int i = 0;i < rr;i++) {
      String s = sc.nextLine();
      int a = 0,b = 0,c = 0,d = 0,e = 0;
      for(int j= 0 ;j < s .length();j++) {
         switch (s.charAt(j)) {
         case 'a': a++;
         break;
         case 'e': b++;
         break;
         case 'i':  c++;
         break;
         case 'o': d++;
          break;
         case 'u': e++;
         break;
         }
      }
      System.out.println("a:"+a);
      System.out.println("e:"+b);
      System.out.println("i:"+c);
      System.out.println("o:"+d);
      System.out.println("u:"+e);
      System.out.println();
   }
sc.close();
   }
}
```

```java
package math;
public class Odd {
    public static void main(String[] args) {
    int x = 6;
    while(x-- > 1) {          
        System.out.print(x); //54321
    }
    System.out.println();
    int y = 6;
    while(--y > 1) {            
        System.out.print(y); //5432
    }
    }
}
```

### switch

```java
package math;
public class Switch {
    public static void main(String[]  args){
        int x = 2,y = 3;
        switch(x)
        {
            default:  //这里如果不加default则会一直运行下去,直到遇到break
                y++;
            case 3:
                y++;
            case 4:
                y++;
        }
        System.out.println("y="+y);           //y=6
    }
}
```

### 打印金字塔Triangle

```java
package math;
import java.util.*;
public class Triangle {
public static void main(String[] args) {
      Scanner input = new Scanner(System.in);
      int s = input.nextInt();
      int j;
      for(int i = 1;i <= s;i++) {
         for( j = 1;j <= (s-i)*2;j++) {
            System.out.print(" ");
            }                                                        
         for( j = i;j > 1;j--) {
            System.out.print(j+" ");
         }
         for(j = 1;j <= i;j++) {
            System.out.print(j+" ");
         }
         System.out.println();
         }
         input.close();
   }
}
```

