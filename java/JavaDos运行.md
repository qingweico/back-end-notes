## 首先 分两种情况 :

### 第一种: 当java源文件中无包时

 直接进入当前要运行的文件目录下, 在目录状态栏中输入cmd进入dos命令窗口

然后输入javac 源文件(后缀名为java) 回车

然后再输入java  源文件(不加后缀名)回车即可 (注意编译的时候要加上文件扩展名java, 而运行字节码的时候则不能加扩展名class)

java命令后面跟的是文件的绝对路径或者是相对路径

javac命令后面跟的是全限定类名

或者直接输入java 源文件.java 也可以

### 第二种: 当java源文件中有包时

先在当前目录编译该文件 在cd..返回上级文件夹（有几级包名就退出几次直到回到包文件所在的目录）

然后再输入java 全限定类名  即可运行

<font style="color:purple;font-size:20px">一定要返回上级目录才可以正常运行,因为一个类名的全称是包名+类名 所以当编译过后JVM会按照当前目录下寻找字节码文件(如果没有配置CLASSPATH路径),而如果不返回上级目录,则会路径重复JVM会报出找不到或无法加载主类的错误 </font>

<font style="color:purple;font-size:20px">如果配置了CLASSPATH路径,则jvm只会到环境变量下的路径寻找字节码文件,找不到则报错,不配置的话则会在当前目录下寻找,一般情况下没有必要配置CLASSPATH</font>

  <font style="color:red;font-size:20px">javap -c 源文件 反编译</font> 



​        

