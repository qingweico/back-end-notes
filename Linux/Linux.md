# Linux

[TOC]

## UNIX发展史

-  1969年 美国贝尔实验室的肯.汤姆森(Ken Thompson)在PDP-机器上开发出了UNIX系统
-  1971年 Dennis M.Ritchie(丹尼斯.里奇)发明了C语言 与ken Thompson一起开发UNIX系统 
-  1973年 UNIX大部分的源码用C语言重写,因此提高了UNIX系统的可移植性

## Linux发展史
   Linux系统诞生于1993年,由芬兰大学生李纳斯(Linus Torvalds)和后来陆续加入的众多的爱好者共同开发完成。
   Linux是开源软件,源代码开放的UnIX

## 支撑互联网的开源技术(LAMP)

   -    LINUX  操作系统
   -    Apache Web操作系统 
   -    MySQL  数据库     
   -    PHP    编程语言

-----

## nano编辑器

## vi 和vim模式的切换

命令行下输入  `vim`  文件名     进入一般模式(正常模式) 可以创建新文件也可以修改已有文件

正常模式下输入i或者a或者o进入编辑模式 退出编辑模式使用`ESC`

正常模式下输入`:`或`/`进入命令行模式 ,退出命令行模式使用`ESC`

### 插入

`:set numer/nu`  设置行号

`:set nonu` 取消行号

`a` 在光标所在字符后插入

`A` 在光标所在行尾插入

`i` 在光标所在字符前插入

`I` 在光标所在行首插入

`o` 在光标下插入新行

`O` 在光标上插入新行

`gg `到文件的第一行

`G `到文件的最后一行

`nG` 到文件的第n行

`:n` 到文件的第n行

`0` 移动到行首

`$` 移动到行尾

-------

### 删除

`x` 删除光标所在处的字符

`nx` 删除光标所在处后的n个字符

`dd `删除光标所在行

`ndd `删除n行

`dG` 删除光标到行尾的所有的内容

`D ` 删除光标所在处到该行行尾所在行的所有内容

`:n1,n2d ` 删除n1到n2的行所有内容

----

### 复制粘贴

`yy` 复制当前行的内容

`nyy` 复制当前以下的n行

`dd` 剪切当前行

`ndd` 剪切当前以下的n行

`p` 粘贴在当前光标的下一行

`P` 粘贴在当前光标的上一行

-----

### 保存

在命令行模式下

 `:wq`  保存当前内容并且退出     

 `:q!`  不保存当前内容且强制退出  

`:q`  是不做任何修改时,退出。若做了修改,需要使用 `:q!` 才可以强制退出

`ZZ` 相当于`:wq` 保存修改并退出

`:w` 保存修改不退出

`:w 指定的目录文件` 另存为指定文件

`:wq!` 文件的所有者以及root可以使用

(带有:的是在命令行模式下,其他都在正常模式下进行,都需要退出插入模式)

### 替换

`R ` 从光标开始处开始进行字符替换,esc结束

`u` 取消上一次操作

`/string` 搜索指定的字符串   `:set ic`  搜索时忽略大小写  `n` 搜索结果向下翻

`%s/替换的目标/替换的值/g`   全文替换指定的字符串   `g` 询问提示  `c` 不询问提示

`8,16s/替换目标/替换的值/g`    在8到16行内替换指定的字符串

### 为vim定义快捷键

`:map Ctrl V Ctrl P I#<ESC> `      ***Ctrl P*** 为光标所在行加上注释并退出

`:map Ctrl V Ctrl B  0x`           ***Ctrl B***      去掉本行的注释 

### 导入命令

`:r 文件目录`                将文件的内容导入此vim编辑器中  

`:r !命令`                     将命令的结果导入vim编辑器中

----

## 关机重启

`shutdown -h now` 立即关机

`halt` 立即关机

`shutdown -h 1` 一分钟后关机

`reboot`  立即重启

`shutdown -r now` 立即重启

##      用户管理

 创建用户:  `useradd 用户名`    (此时默认的分组名是用户名)

 设定密码  : `passwd 用户名 `  

 指定组名 : `useradd -d 组名 用户名` 

`who` : 查看用户登陆信息

![1588911447007](https://static-i0.oss-cn-shanghai.aliyuncs.com/pic/5ef2e89f14195aa594bff21f.pngF)

第一个root 代表服务器启动时root登陆

第二个root代表远程登陆(XShell)

第三个root代表服务器启用终端登陆

:0代表本地登陆

pts代表远程登陆 使用不同的数字区分不同的远程终端

`w` :查看用户登陆的详细信息

![1588911970296](https://static-i0.oss-cn-shanghai.aliyuncs.com/pic/5ef2e89f14195aa594bff221.png)

`uptime` 查看用户登陆一些基本信息

### 删除用户

`userdel 用户名` 

### 查询用户信息

`id 用户名`  

### 切换用户

`su - 用户名` 

高权限用户向普通用户切换时不需要密码 反之则需要

`exit` 返回之前用户

### 创建组

`groupadd  组名`

`useradd - g 组名 用户名` :将用户分配到指定的组中

`-d` 和`-g`给用户分配组名的区别: 前者给用户创建一个之前不存在的组名,后者给用户分配已经创建完成的组

### 删除组

`groupdel 组名 ` 

### 更换用户所在组

`usermod -g :新的组名 用户`

### 用户和组的相关的文件

`/etc/passwd` : 用户的配置文件 记录用户的各种信息

`/etc/shadow` : 口令的配置文件

`/etx/group` : 组的配置文件 记录linux包含组的信息

## Linux运行级别

- 0 系统停机状态  系统默认运行级别不能设置为0,否则不能正常启动
- 1 单用户工作状态,root权限,同于系统维护,禁止远程登陆
- 2 多用户状态(没有NFS),不支持网络
- 3 完全的多用户状态(有NFS),登陆后进入控制台命令模式
- 4 系统未使用,保留
- 5 Xll控制台,登陆后进入图形GUI模式
- 6 系统正常关闭并重启。默认级别不能设为6,否则不能正常启动

`runlevel` :查看当前运行级别

## 帮助指令

`man [指令] /配置文件 `  查看命令或者配置文件的帮助信息,当查看配置文件时不用加配置文件的绝对路径 ,直接加配置文件名即可 

`whatis 命令 `   查询命令的简单介绍

`apropos 配置文件`  查询配置文件信息

`命令 --help` 获取命令的主要的信息

`info 命令` 和man差不多   

`help 命令` 查看linux内置的命令  

## 实用指令

`usr` : unix system resource

`var` : Variable Data File 存储运行时数据

`dev` : echo "..." > /dev/null 可以向其中写入任何东西(好比垃圾桶)

### 文件目录类

`pwd`:  显示当前工作目录的绝对路径

`ls [选项] 【文件或目录】`

选项包括 `a`, `l`

`ls -l` : 表示以列表的形式显示当前目录下所有的文件

`ls -a` 表示显示当前目录下所有的文件包括隐藏的文件或文件夹

`ls -d` 显示当前目录的信息

`ls -F` 查看文件的类型

#### 创建目录和删除

`mkdir 目录名     `     创建一层目录

`mkdir -p 目录名` 创建多级目录

`rmdir 目录名`  删除空的目录

`rm -rf 目录名 ` 删除不为空的目录

#### 创建空的文件

man touch:  touch - change file timestamps

确实tocuh的作用是改变文件的时间戳 如果没有则创建文件

`touch 文件名` 

#### 复制文件

`scp file_source file_target` 用于Linux之间复制文件和目录

eg: `scp local_file remote_username@remote_ip:remote_folder ` 从本地复制到远程

`cp resource target`   将resource复制到目标目录下

`cp -r resouce taget` 使用递归将文件夹下所以的文件全部复制到目标目录下

`\cp -r resouce taget` 强制覆盖以存在的文件

`cp -p` 保存文件的原有属性

#### 删除文件

`rm 【选项】文件名或目录名`

选项 :`-r` 删除整个目录   `-f` 强制删除没有提示信息 

`mv` 重命名文件或者移动文件

当在同一个目录下移动文件就是重命名  `mv oldFileName newFileName`

当前后目录不同时就是移动文件夹  `mv oldFileName /root/`;将oldFileName移动到root目录下  

#### 查看文件

cat用于连接文件并打印到标准输出设备上

查看日志文件不要使用cat 如果日志文件过大会卡死 应该使用less命令

`cat 【选项】文件名 | more `  :只读的方式 | 分页的形式查看文件  选项为 `-n`: 可以显示行号 

more指令

`more 文件` :分页的形式查看文件

`enter` :一行一行看

`空格` 一页一页查看

`ctrl+B` : 查看上一页

`ctrl+f或f` 查看下一页

`q或Q` 立即离开文件内容

`=` 显示当前的行号

`:f` 显示文件名和当前的行号

less指令

`less 文件`  方便查看大型文件 ,一次只加载一页 

`enter` 一次查看一行

`空格` 一次查看一页

`上箭头` :向上翻页

`下箭头` 向下翻页

`q` 退出该文件

`/字符串` 向下查找字符串  `n ` 向下查找 `N` 向上查找

`?字符 `串 向上查找字符 `n` 向上查找 `N` 向下查找

#### ` >` 和 `>>` 指令

`文件 >` 文件  覆盖写     指令的意思是将左边的文件写入到右边的文件中 若右边的文件不存在则创建 存在则覆盖

`文件 >> 文件` 追加写

`ls -l > 文件` 将当前目录下的文件列表写入到文件中

`cat 文件1 > 文件2` 将文件1的内容覆盖写到文件2  (  >> 追加写入)

`echo "内容" > 文件` 将内容覆盖写入文件中   ( >> 追加写入)

#### echo

`echo  输入内容` : `echo $PATH` 输入环境变量

`echo hello` 向控制台输入hello

`printf '%x\n'255` 将十进制转换为16进制打印

#### head&tail

`head 文件 `             查看文件的前(默认)10行

`head -n N 文件`  查看文件的前N行

`tail 文件`            查看文件的后(默认)10行

`tail -n M 文件`  查看文件的后M行

`tail -f  文件`    实时追踪该文档的所有更新, 工作中很常用   

#### ln指令 (软连接)

```bash
ln -s /root linkToRoot                # 将linkToRoot链接到root目录下
ln  /root/hard  /root/hard/linkHard   # 硬连接  可以做到数据的同步更新且硬连接的inode和源文件相同 但是硬连接不能对目录使用且不可以跨分区
```

当我们使用pwd查看绝对路径时仍然是当前路径

`rm - rm linkToRoot` 删除软链接

#### 软硬链接的区别

```shell
# -s : 建立软链接文件 如果不加 -s 选项 则建立硬链接文件
ln [选项] 源文件 目标文件
硬链接 只能应用于文件 而不能应用于目录 而且不能跨文件系统(即分区) 即不可以在不同文件系统的文件间建立链接
软链接 : 可以跨文件系统
```

#### alias指令

`alias` : 列出目前所有的别名

给命令设置别名(服务器重启后该设置失效): alias croot=cd /root

通过修改配置文件的方式(永久生效)

```shell
vim ~/.bashrc
```

```shell
# .bashrc

# User specific aliases and functions

alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'
alias croot='cd /root'

# Source global definitions
if [ -f /etc/bashrc ]; then
        . /etc/bashrc
fi
```

```bash
# 让配置生效
source ~/.bashrc
```

#### history指令

`history` 显示所有的历史命令

`history M` 显示前M个历史指令

`!N` 执行第N个历史指令

### 时间日期类

#### date指令

`date` 显示当前的时间

`date"+%Y-%m-%d %H:%M:%S"` 格式化日期显示时间  (+号不可省略)

`date -s "时间"` 修改时间  

`cal` 显示当前月份的日历

`cal 年份` 显示一年的日历

`timedatectl set-time 2018-01-03 23:00:00` 设置当前系统时间

### 搜寻查找类

`find 【目录】 查找类型 文件名`   

```bash
find /home -name hello.txt  # 查找home目录下文件名为hello.txt的文件
* 匹配任何字符
？匹配任意字符
-iname 不区分大小写
```

```bash
find /home -user admin # 查找用户名为admin的文件
```

```bash
find / -size +20M  # 查找当前系统下大于20M的文件  -20M(小于20M的文件)   20M(等于20M的文件)  (M大写 k可以小写 不可以大写)
```

``` bash
find /etc -cmin -5;# 在etc目录下查找5分钟内被修改过文件属性的文件和目录
-amin  访问时间  access
-cmin 文件属性   change
-mmin 文件内容   modify
```

`-a` and   同时满足两个条件

`-o` or     两个条件满足其中一个即可

`-type 【选项】` 【选项】:`-f` 普通文件  `-d` 目录  `-l` 软连接

```bash
find /etc -size +20M -a -size -50M -type f          # 查找etc目录下文件大小在20M~50M之间的文件
```

```bash
find /etc -name *init* -exec 执行操作命令 {} \;      # 对搜索结果进行操作(严格区分空格)
```

```bash
find /etc -name *init* -ok 执行操作命令 {} \;        # 带有询问
```

*![1588829333905](https://static-i0.oss-cn-shanghai.aliyuncs.com/pic/5ef2e89f14195aa594bff21d.png)*

找到test目录下java的文件且带有询问的删除

```bash
find . -inum inod号 -exec rm -rf {} \; # 根据inode号来删除当前目录下的文件
```

#### locate

`locate 文件` 用于快速定位文件 基于数据库查找    不用遍历整个文件系统

刚创建的文件,需要使用命令`updatedb`更新数据库,才可以查找到该文件

在/tmp中创建的文件不可以被locate找到

#### grep和管道符

管道是进程中通信的手段 也是一个文件 

`mkfifo filename `创建一个管道文件

`ls / | tee file | wc -l`

`grep` 过滤查找

`grep password filename` 在文件中过滤查找

`|`  将前一个命令查找的结果输出传递给后一个命令输入

`cat hello.txt| grep -n hello`  查找hello.txt文件 并且在该文件中查找hello并显示所在的行号

`-n ` 显示行号

`-i` 忽略大小写

`-v 字符串` 排除指定的字符串  `^N`   以N开头的字符串 

#### whereis  which

`which` 用来查看命令的文件路径以及是否有别名

`whereis` 用来查看命令所在的目录以及帮组文档的路径

#### 高阶查找

xargs  配合管道符一块使用

find 文件目录 -name "*.java" | xargs -I {} cat {} | wc -l 查看目录中以java结尾的文件中所有的行数

### 解压和压缩类

`gzip 文件` : 压缩    

`gunzip 文件.gz` 解压(不常用)  使用`gzip`命令压缩文件时不会保留源文件且不可以对目录压缩

----

`zip 【选项】文件 压缩目录`              压缩        选项可以为 `-r` 递归压缩整个目录 文件是压缩后的文件名

`unzip【选项】解压目录 文件`            解压        选项可以为`-d<目录>` 指定解压后存放的目录  文件是解压前的文件名

---

`tar `   打包的文件以.tar.gz结尾

`c` 产生.tar打包文件

`v` 显示详细信息

`f` 指定打包的文件名

`z` 打包同时压缩

`x` 解压.tar文件

`tar -zcvf 压缩后的文件名 目标文件名/目录`                                         压缩文件

`tar -zxvf 将要解压的文件 解压到的目录`                                               解压文件

-----

`bzip2` zip的升级班 增加了可以保留源文件的选项

`bzip2 -k  源文件`  压缩文件并保留源文件

`bunzip2 -k 压缩文件`  解压文件且保留压缩包

----

`tar -cjf 压缩后的文件名 源文件`  生成.tar.bz2的压缩文件

`tar -xjf` 解压以.tar.bz2结尾的压缩文件

`tar -tvf backup.tar ` 查看tar文件的内容而不提取

`tar -zxvf backup.tar -C /usr/local` 解压到其他目录

### 排序类

```bash
# ip.txt,每行一条ip记录,共若干行,已排好序,统计出现次数最多的前3个ip及其次数
uniq -c ip.txt | sort -nr | head -n 3
```

## Linux组管理

### 查看文件的所有者

`ls -ahl`

### 修改文件的所有者

`chown 用户名  文件名  `             改变单个文件的所有者

`chown -R 用户名 文件目录名`  改变目录下的所有文件或目录(递归) 的所有者

### 修改文件所在组

`chgrp 新组名 文件名 `                     改变单个文件的所在组

`chgrp -R 新组名 文件目录名`       改变目录下所有文件索目录的所在组

### 改变用户所有组

`usermod -g  新组名 用户名`

### 查看缺省文件创建的权限

`umask -S`

### 权限

*![1588306848988](https://static-i0.oss-cn-shanghai.aliyuncs.com/pic/5ef2e89114195aa594bfed34.png)*

*![1588307060227](https://static-i0.oss-cn-shanghai.aliyuncs.com/pic/5ef2e89114195aa594bfed37.png)*

从左到右:

- `d`  代表文件类型是目录  `-` 代表普通的文件 `l`  代表软连接  `c`  字符设备【键盘,鼠标】  `b`  块文件 硬盘
- `rwx`  表示文件所有者的权限  `r ` 代表写的权限  `w` 代表读的权限  `x(execute)` 代表可执行的权限
- `r-x` 代表与文件拥有者同一组的用户所拥有的权限  可以写和执行但不能读
- `r-x` 代表不与文件拥有者同组的其他用户所拥有的权限 (以上3个一组  `-` 代表不具有该权限    `rwx` 可以用数字表示 `r` 等于4   `w`  等于2    `x`等于1)

- 3或者1  如果是文件 数字代表硬连接数目 如果是目录则代表该目录下的子目录数(不包括文件 包括隐藏目录)
- root 代表文件的所有者
- root 代表文件所在的组名
- 19    如果是文件 表示文件的大小(字节)   如果是目录 
- 时间代表最后一次修改的时间

### rwx权限详解

rwx作用文件:

- r代表read 可以读取,查看
- w代表write 可以修改,但是不代表可以删除该文件,删除一个文件的前提是要有对该文件有写的权限,才可以删除该文件
- x代表可执行,可以被执行

rwx作用目录

- r代表可读  ls可以查看目录内容
- write代表可写 代表可以修改  在目录内创建-删除-重命名目录
- x代表可以执行 可以进入该目录

***删除一个目录下文件的关键不是该文件是否具有w(写)权限,而是该文件所在的目录是否具有w权限即对文件的创建,删除权限***

### 修改权限 (通过 +  -  =  变更权限)

`u ` 代表所有者 `g` 代表所有组 `o`代表其他人  `a` 代表所有人(u+g+o)

`chmod u=rwx,g=rx,o=x  文件目录名`  将该文件的权限修改为 rwxr-x--x

`chmod o+w 文件目录名`  增加其他人对该文件读的权限

`chmod a-x ` 移出所有人对该文件的执行权限

`chmod -R` 遍历修改目录下所有文件或者目录的权限

 ###  修改权限 (通过数字变更权限)

`chmod  751  文件目录名`    7 = `r+w+x`  5  = `w+x`  1 = `x`

## 任务调度

任务调度是指系统在某个时间执行特定的命令或者程序

`crontab -e` 编辑定时任务的

`crontab -l` 查询crontab的任务

`crontab -r` 删除当前用户下所有的crontab任务

`tail -f /var/log/cron` 查看定时任务的执行日志

`*/1 * * * * ls-l >/tmp/to.txt`:    每小时的每分钟执行`ls-l >/tmp/to.txt `命令

### 5个占位符说明

| 项目    | 含义               | 范围                |
| ------- | ------------------ | ------------------- |
| 第一个* | 一小时中的第几分钟 | 0-59                |
| 第二个* | 一天的第几小时     | 0-23                |
| 第三个* | 一月的第几天       | 1-31                |
| 第四个* | 一年的第几个月     | 1-12                |
| 第五个* | 一周的星期几       | 0-7(0和7都代表周日) |

## 磁盘查询指令

`df- lh` : 查询系统整体磁盘使用的情况	

`du-h /目录` : 查询指定目录的磁盘占用情况  默认为当前目录

- -s 指定目录占用大小总汇
- -h带计量单位
- -a含文件
  - --max-depth=1 子目录深度
- -c 列出明细的同时,增加汇总值

### 统计目录下文件的个数

`man wc ` 查看wc命令的用法

`ls -l /home |grep "^-"| wc -l` 统计home目录下文件的个数

### 统计目录下目录的个数

`ls -l /home |grep "^d"| wc -l` 统计home目录下目录的个数

### 统计目录下所有的文件个数(包括子文件)

`ls -lR /home |grep "^-"| wc -l` 统计home目录下所有的文件个数

### 统计目录下所有的目录个数(包括子目录)

`ls -lR /home |grep "^d"| wc -l` 统计home目录下所有的目录个数

## 网络命令

`ip`

`last` 查看目前和过去所有登陆过系统的用户的信息

`lastlog -u 用户id号` 查看具体用户登陆的详细信息

`traceroute 主机` 查看数据包到路径之间的路径   

`ss` 用来显示已建立连接的所有套接字的列表

`netstat` 显示网络有关的信息

- `-a` 显示所有选项 默认不显示LISTEN相关
- `-t` TCP协议
- `-u` UDP协议 UDP 是User Datagram Protocol的简称, 中文名是用户数据报协议
- `-l`  监听
- `-r` 路由
- `-n` 显示IP和端口号
- `-p` 显示建立相关链接的程序名

`netstat -ntlp` 查看本机监听的端口及应用名称

`netstat -tlun` 查看本机监听的端口

`netstat -an` 查看本机所有的网路连接

`netstat -rn` 查看本机路由表

`setup` 配置网络

`mount` 挂载命令

`tcpdump -nn -i eth0 port 80  ` 对网络上的数据包进行截获的包分析工具

`route -n` 显示和查看路由表  `-n`表示不解析名字

`arp -n` 显示和修改ARP

`dig`

`curl www.baidu.com -I` 查看http的头部信息

`nc`  nc是netcat的简写 是一个功能强大的网络工具

- 实现任意TCP/UDP端口的监听
- 端口的扫描
- 机器之间传输文件
- 机器之间网络测速

## 进程管理

在linux中 服务命令中带有d(daemon)表示后台进程 >> mysqld

ps命令是用来查看目录中,有哪些正在执行,以及他们执行的状况,可以不加任何参数

`ps -a` : 显示当前终端所有进程信息

`ps -u`: 以用户的格式显示进程

`ps -x`: 显示后台进程运行的参数

![1588383393872](https://static-i0.oss-cn-shanghai.aliyuncs.com/pic/5ef2e89114195aa594bfed39.png)

- user:用户名
- PID:进程的id
- %CPU:进程占用CPU的百分比
- %MEN:进程占用物理内存的百分比
- VSZ:进程占用虚拟内存的大小(KB)
- RSS:进程占用的物理内存大小(KB)
- TTY:终端名称缩写
- STAT:进程状态。S表示休眠  s表示该进程是会话的先到程序  N表示进程拥有比普通进程优先级更低的优先级   R表示正在运行 D表示短期等待 Z 表示僵死进程 T表示被跟踪或被停止
- START:进程的启动时间
- TIME:进程使用CPU的总时间
- COMMAND:启动进程所用的命令和参数,如果过长会被截断显示

### 查看单个进程

`ps -aux | grep 单个进程的名称`

`ps -aux | grep 单个进程的名称| grep -v grep`: 过滤掉grep操作

### 查看父进程

`ps -ef`: 以全格式显示当前所有的进程,查看进程的父进程 `-e`:显示所有进程 `-f` :全格式

![1588384476648](https://static-i0.oss-cn-shanghai.aliyuncs.com/pic/5ef2e89114195aa594bfed3b.png)

- UID: 用户ID
- PID: 进程ID
- PPID: 父进程ID
- C: CPU用于计算执行优先级的因子。数值越大,表明进程是CPU密集型运算,执行优先级会降低,数值越小,表明进程是I/O密集型运算,执行优先级会提高

### 终止进程

`kill 进程号`  

强制非法用户下线

![1588386912261](https://static-i0.oss-cn-shanghai.aliyuncs.com/pic/5ef2e89114195aa594bfed3d.png)

*![1588387069698](https://static-i0.oss-cn-shanghai.aliyuncs.com/pic/5ef2e89f14195aa594bff219.png)*

`killall 进程名称` :            通过名称终止多个进程

`kill -9 进程号`   :             强制终止某个进程

### 查看进程树

`pstree 【选项】`

【选项】  `-p`   显示进程的PID    `-u  ` 显示进程的所属用户名

### strace命令

## 服务管理

`systemctl status firewalld`  查看当前防火墙的状态(重启restart,终止stop,开始,start)

### 查看服务名

`setup`  或者  `systemctl  list-unit-files` 

### 动态监控服务

`top -u 用户名`: 监视特定用户

![1588392949656](https://static-i0.oss-cn-shanghai.aliyuncs.com/pic/5ef2e89f14195aa594bff21b.png)

`top 回车 k 回车 进程号 `: 选择要终止的进程

`top -p 进程号` 单独监控该进程 继续输入`H` 则获取当前进程下所有的线程信息

**设定动态刷新的时间(默认是3秒)**

`top -d 时间间隔(s)`

**字母q可以退出动态监控界面**

### 监控网络状态

`netstat -anp` 查看系统所有的网络服务 `-an`:按一定顺序排列输出 `-p` :显示哪个进程在调用  `-t ` :显示TCP传输协议的连线状况

`netstat -anp | grep 网络服务名称` 查看单个网络服务

## rpm

安装未安装的rpm包时,使用rpm包全名且后跟rpm包的路径,而查询或者卸载时直接使用包名,系统会从数据库中寻找该rpm包的信息,所以可以在任意位置查询或卸载该rpm包

*![1589100480404](https://static-i0.oss-cn-shanghai.aliyuncs.com/pic/5ef2e8a614195aa594bff3e6.png)*

```bash
rpm -qa | grep 包名;  # 查询rpm安装包
rpm -e --nodeps 包名 ;# 强行卸载rpm安装包  erase
rpm -ivh rmp包全名;   # 安装rpm文件
```

`-i` 代表安装(install)

`-v` 代表显示详细信息 verbose

`-h ` 显示详细信息 hash

`--nodeps` 不检测依赖性

`rpm -ql` 查询rpm包的安装位置   `l` list    `q`  query   `p`  package查询未安装包将要安装的信息(加全包名)

`rpm -qf 系统文件` 查询系统文件属于那个rpm包

`rpm -qR 包名` 查询已安装包的依赖

`rpm -qRp 包全名` 查询未安装包的依赖 R requires

## yum

***yum只是在线管理rpm包的命令,并不存在yum包,可以解决包和包之间的依赖***

`yum list | grep 包名` 查看系统是否存在该rpm包

`yum install 包名` 安装rpm文件

`yum remove 包名 `    卸载rpm包 

`yum update 包名`  升级rpm包 ***不加包名会升级所有的rpm包括linux内核,导致系统崩溃***

`-y 选项`  自动回答yes

`yum install -y man-pages` 安装函数使用手册

`yum install -y lsof`

`yum install -y nc` 

## Tool

![linux_observability_tools](https://static-i0.oss-cn-shanghai.aliyuncs.com/pic/linux_observability_tools.png)
