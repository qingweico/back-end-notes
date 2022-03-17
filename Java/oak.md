## 目录

[TOC]

### 二维数组

```java
public class ArrayDimensional {
	public static void main(String[] args) {
		Scanner sc = new Scanner(System.in);
	    int [][]arr = new int[3][2];
	    for(int i = 0;i < arr.length;i++) {
		   for(int j = 0;j < arr[i].length;j++) {
			 arr[i][j] = sc.nextInt();
		   }
	     }
		for (int[] ints : arr) {
			for (int anInt : ints) {
				System.out.println(anInt);  //增强for循环遍历的对象数组不可以为null
			}
		}
	sc.close();
  }
}
```

### 生成随机字节数组

```java
public class ArraysToString {
   static Random rand = new Random();
   public static void main(String[] args) {
      byte[] by = new byte[10];
      rand.nextBytes(by);
      System.out.println(Arrays.toString(by));
   }
}
```

### 倒置数组

```java
public static void reverse(int []arr) {
    for(int start = 0,end = arr.length-1;start <= end;start++,end--) {       //倒置数组函数
        int temp = arr[end];
        arr[end] = arr[start];
        arr[start] = temp;
    }
}
```

### 字符串倒置

```java
public static String reverse(String str) {
    StringBuilder newStr = new StringBuilder();
    for (int i = str.length() - 1; i >= 0; i--) {
        newStr.append(str.charAt(i));
    }
    return String.valueOf(newStr);
}
```

```java
char[] ch = str.toCharArray(); //字符串转换为字符数组
System.out.println(Arrays.toString(ch));
```

```java
byte[] bs = str.getBytes();  //字符串转换为字节数组
System.out.println(Arrays.toString(bs));
```

### 字符串和字节之间的转化

```java
public class StringGetBytes {
   public  static void main(String[] args) {
      String s = "你好";
      byte[]ss = s.getBytes(StandardCharsets.UTF_8);      //字符串转换为字节
      System.out.println(Arrays.toString(ss));            //以字节数组的形式输出
      String sss = new String(ss, StandardCharsets.UTF_8);//将字节数组转换为字符串
      System.out.println(sss);                            //默认GBK编码
      //-------------------------------------------------
      //UTF-8编汉字占用3个字节
      //UTF-16编码汉字和英文都占用2个字节
      //GBK编码汉字则占用2个字节
      //GBK相当于ASCII的扩展
      //范围大小: GB2312<GBK<GB18030
   }
}
```

### 输入数组的大小并排序

```java

public class ArrayList_Sort {
    public static void main(String[] args) {
        List<Integer> list = new ArrayList<>();
        Scanner sc = new Scanner(System.in);
        while(true) {
            int number = sc.nextInt();
            if(number == 0) {
                break;
            }
            else {
                for(int j = 0;j < number;j++) {
                    list.add(sc.nextInt());
                }
            }
            Integer[] temp = new Integer[list.size()];
            Integer []arr = list.toArray(temp);
            Arrays.sort(arr);
            list.clear();
            for(int data:arr) {
                System.out.print(data + " ");
            }
            System.out.println();
        }
        sc.close();
    }
}
```

### 随机数

```java
Random s=new Random();
s.nextInt(20);  //指定随机数的范围
Math.random();  //产生0到1的随机浮点数,包括0不包括1
```

### 大整数和大浮点数

```java
BigInteger i = new BigInteger("2147483648");                 //大整数
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
