```shell
#预处理器阶段
g++ -o Helloworld.i -E HelloWorld.cpp
```

```shell
#编译期阶段
g++ -o HelloWorld.s -S HelloWorld.i
```

```shell
#汇编器阶段
g++ -o HelloWorld.o -c HelloWorld.s
```

```shell
#连接器阶段
gcc -o Helloworld.exe HelloWorld.o
```

预处理器、编译期、汇编器以及链接器一起构成了编译系统

