### 精通Linux 第二版

##### 1 linux层次

三层： 最底层是硬件 硬件之上是内核 内核是操作系统核心  内核是运行在内存中的软件  

内核向中央处理器发送指令 内核管理硬件系统  是硬件系统和应用程序之间进行通信的接口  

进程是指计算机中运行的所有程序 进程由内核统一管理 进程组成最顶层 称为用户空间  


用户进程  


linux内核： 系统调用  进程管理  内存管理  设备驱动程序

硬件： cpu  内存  硬盘  网络端口  


内核和用户进程之间最主要的区别： 内核在内核模式 Kernel mode中运行  用户进程则在用户模式 user mode中运行  

内核模式中运行的代码可以不受限的访问cpu和内存 功能强大  非常危险  那些只有内核可以访问的空间称为内核空间 kernel space  

内核的内存管理  

内核将内存划分为很多区块 并且一直维护这些区块的状态信息  

内核负责管理四个方面：

* 进程 内核决定那个进程可以使用cpu

* 内存 内核管理所有内存 为进程分配内存 管理进程间的共享内存以及空闲内存

* 设备驱动程序 作为硬件系统与进程之间的接口 内存负责操控硬件设备

* 系统调用和支持 进程通常使用系统调用和内核进行通信


进程管理  

管理涉及到：进程的启动  暂停  恢复和终止  

一个进程让出CPU使用权给另一个进程称为上下文切换 context switch  

系统是在同时运行多个进程称之为多任务执行

内核负责上下文切换  其工作原理如下：

1. cpu为每一个进程及时 到时即停止进程 并切换至内内核模式---由内核接管CPU控制权  

2. 内核记录当前cpu和内存的状态信息 这些信息在恢复被停止的进程时需要用到

3. 内核执行上一个时间段内的任务

4. 内核准备执行下一个进程  从准备就绪的进程中选择一个执行  

5. 内核将新进程准备cpu和内存  

6. 内核将新进程执行的时间段通知cpu

7. 内核将cpu切换至用户模式 将cpu控制权移交给新进程  

内核是在什么时候运行的？ 内核在上下文切换时的时间段间隙中运行的  


内存管理  

内核在上下文切换过程中管理内存  

内核要保证以下所有条件：  

* 内核需要自己的专有内存空间 其他用户进程无法访问  

* 每个用户进程有自己的专有内存空间  

* 一个进程不能访问另一个进程的专有内存空间

* 用户进程之间可以共享内存  

* 用户进程的某些内存空间可以是只读的

* 通过使用磁盘交换 系统可以使用比实际内存容量更多的内存空间

Memory Management Unit 内存管理单元 MMU
MMU使用了一种叫作虚拟内存的内存访问机制  即进程不是直接访问内存的实际物理地址  而是通过内核使得进程看起来可以使用整个系统的内存  
即进程访问内存的时候 MMU截获访问请求 然后通过内存映射表将要访问的内存地址转换为实际的物理地址  

内核需要初始化 维护和更新这个地址映射表。例如在上下文切换时 内核将内存映射表从被移出进程转给被移入进程使用  

设备驱动程序和设备管理  

1. 通常设备只能在内核模式中被访问 因为设备访问不当有可能会让系统崩溃  
2. 不同设备之间没有一个统一编程接口 设备驱动程序会尽可能为用户进程提供统一的接口  以简化开发人员的工作  

系统调用和系统支持  

系统调用 system call或syscall 为进程执行一些它们不擅长或无法完成的工作  例如 打开  读取  写文件  

fork 当进程调用fork()时 内核创建一个和该进程几乎一模一样的副本  

exec 当进程调用exec(program)时 内核启动program来替换当前的进程  

Linux中的所有的用户进程都是通过fork()来启动的 除了创建现有进程的副本外  大多数情况下你还可以使用exec()来启动新进程  

通过ls命令说明：

通过终端输入ls时 终端窗口中的shell调用fork()创一个shell的副本  
然后这个shell副本 调用exec(ls)来运行ls   

虚拟设备  

虚拟设备对于用户进程而言是物理设备 但是通过软件来实现的 因此从技术角度来说  它们并不需要存在于内核中 但是实际上它们很多都存在于内核中。  


用户空间  

内核分配给用户进程的内存空间 称为用户空间 userland 

一个进程就是内存中的一个状态 用户空间也可以指所有用户进程占用的所有内存  

用户  

linux内核支持用户这一Unix的传统概念 一个用户代表一个实体  用户有权限运行用户进程 对文件拥有所有权  
每个用户都有一个用户名 然后内核是用户ID来管理用户的 用户ID是一串数字标识  

用户机制主要权限管理  每个用户进程都有一个用户作为所有者 在一定限制条件下 用户可以终止和改变自己的进程的行为  
但对其他用户的进程无权干预  用户可以决定是否将属于自己的文件和其他的用户共享  但是root例外 

##### 2 基础命令 目录结构

1. shell /bin/sh

shell 命令行界面  是运行命令行的应用程序  命令行就是用户输入的哪些命令  

提示符格式： name@host:$path$

$后面是命令

2. cat 显示一个或多个文件 


3. cat 不指定文件名 就从linux内核提供默认标准流中获得输入数据 这时运行cat命令的终端就成为标准输入  

ctrl+d 终止当前终端的标准输入并终止命令 通常会终止一个程序

ctrl+C 终止当前进程

内核为每个进程提供一个标准输出流供进程输出数据  cat命令将数据输出到标准输出  

标准输入和标准输出 stdin stdout

4. ls 显示目录内容  

5. cp 拷贝

```
cp file1 file2 将文件1复制到文件2

cp file1 ... fileN dir 将文件们复制到指定目录
```

6. mv 重命名 移动文件

```
mv file1 file2 将文件1改名为文件2

mv file1 ... fileN dir 将文件们移动到指定目录

```

7. touch 创建文件

8. rm 删除文件

9. echo 输出


目录结构  

/ 根目录  

.. 上一层目录  

. 当前目录  

绝对路径  从根目录开始  

相对路径  不从目录开始  


10. cd 设置当前目录  

11. mkdir 创建目录  

12. rmdir 删除空目录  

13. rm -rf dir 真正目录

14. 通配符  shell中可以使用通配符来匹配文件名和目录名  

shell根据参数中的通配符来匹配文件名  shell命令中的参数替换为实际的文件名  这个过程称为展开  


* 代表任意字符和数字    

? 代表一个字符和数字  

如果不想让通配符展开 就可以用单引号 ''  

15. grep 显示文件和输入流和参数匹配的行  

```
grep root /etc/passwd
显示passwd文件中所有含有root的行

grep root /etc/*
显示etc目录下所有含有root的文件

-i 不区分大小写

-v 反转匹配 

-E 识别正则表达式

```

16. less 分屏显示 空格键 下一屏 B 上一屏 Q 退出

17. pwd 当前目录  

18. diff 查看文件间不同

```
diff -u file1 file2


```

19. file 查看文件格式信息  UTF-8 Unicode text

```
file  filename

```

20. find  在特定目录中查找文件

```
find dir -name file -print

```

在find可以使用模式匹配参数 但是必须加引号 以免shell展开

21. locate 

locate与find的区别 locate通过系统创建文件索引查找 比find快 但是新文件无能为力 因为新文件不会被系统立即索引

22. head 文件前10行 tail 最后10行 

-n 设置显示行数

head -n 5 /etc/passwd  前5行

tail -n +k 显示从k行以后所有内容

23. sort 对文件内所有行按照字母顺序快速排序 

使用参数 -n 按照数字顺序排序 那些以数字开头行

使用参数 -v 反向排序

24. passwd 更改密码

25. chsh 更改shell 

26. dot 文件

dot文件特别之处 ls只有加 -a 才显示 shell通配符不匹配dot文件 除非明确指定.*  

.* 会匹配 .当前目录 ..上级目录   可以使用正则表达式 .[^.] * 或 .??* 来排除这两个目录  

27. shell变量 

shell可以保存一些临时变量 称作shell变量  

使用等号给shell变量赋值  

```
STUFF=blash

```

用$STUFF来获得该变量的值  

28. 环境变量

环境变量不仅针对shell linux系统中所有进程都能访问环境变量  

环境变量与shell变量的区别:  

shell变量只能在当前的shell访问  

环境变量能够被shell中运行的所有进程访问  

环境变量可以export设置  

```
STUFF=blash

export STUFF
```

29. 命令路径

PATH是一个特殊的变量 PATH定义了命令路径 简称路径  

命令路径是一个系统目录列表 shell在执行一个命令的时候 会去这些目录中查找这个命令  

```
echo $PATH

```
路径之间以冒号 : 分隔

设置路径 PATH=$PATH:dir 或 PATH=dir:$PATH  

30. 特殊字符

\* 通配符  

. 当前目录  

! 逻辑非运算符 命令历史  

| 命令管道  

/ 目录分隔符 搜索命令  

\ 常量 宏  

$ 变量符号  行尾  

' 字符串常量  

` 命令替换  

" 半字符串常量  

^ 逻辑非运算符  行头  

~ 逻辑非运算符 home目录快捷方式  

\# 注释 预处理  替换  

[] 范围  

{} 声明块  范围  

_ 空格的简易替代  

31. 命令行编辑 


ctrl+b 左移光标  

ctrl+f 右移光标  



ctrl+p 查看上一条命令 上移光标  

ctrl+n 查看下一条命令 下移光标  



ctrl+a 移动光标至行首   

ctrl+e 移动光标至行尾  



ctrl+w 删除前一个词    

ctrl+u 删除从光标至行首的内容    

ctrl+k 删除从光标至行尾的内容   

ctrl+y 粘贴已删除的文本  

32. 文本编辑器  

vi vim Emacs  

33. 获取在线帮助  

man command  

不知道明确命令 通过关键字 -k  

man -k keyword  

按照章节  

1 用户命令  

2 系统调用  

3 unix高级编程库文档  

4 设备借口和设备驱动程序信息  

5 文件描述符  系统配置文件  

6 游戏  

7 文件格式 规范 编码  

8 系统命令和服务器 

34. info命令  

info  command  

35. shell输入和输出  

重定向  > 

command > file  将命令的执行结果输出到文件中  

如果文件不存在  shell会创建一个文件  

如果文件存在  shell会先清空文件  

如果不想把源文件覆盖 使用 >> 会将命令的输出结果加入到文件尾  

管道符 | 将一个命令执行结果输出到另一个命令  

重定向标准错误输出  2>  

>& 将标准输出和标准错误输出重定向到同一个地方  

36. 标准输入重定向  

```
head < file 

将file输入head命令 

```

37. 理解错误信息  

常见错误  

* 文件、目录错误  

* 权限错误  

* 分段故障  总线错误  

38. 查看和操作进程  

进程是运行在内存中的程序  每个进程都有一个数字ID 叫进程ID  ProcessID pid  

ps 列出所有进程  

pid  进程号  

tty  进程所在的终端设备  

stat 进程状态  S 睡眠  R 运行  

time  进程到目前为止所用cpu时长   mm:ss   

cmd 命令名  command   

命令选项  

ps  x  显示当前用户运行的所有进程  

ps  ax 显示系统当前运行的所有进程 包括其他用户的进程  

ps  u  显示更详细进程信息  

ps  w  显示命令的全名  而非仅显示一行以内的内容  

所以 ps aux  

ps u $$   $$ 表示当前的shell进程  

终止进程  用kill命令向进程发送一个信号 信号是内核发给进程的一条消息  

kill pid   

信号很多 默认是TERM terminate 

kill -stop pid 让进程暂停  

kill -cont pid 被暂停的进程仍然驻留在内存  等待被继续执行 使用cont信号可以继续执行进程  

kill是终止进程最粗鲁的方式  kill会强行终止进程  并将其移除内存  不会给进程清理和收尾的机会  

可以使用数字来代替信号名 kill -9  === kill -KILL 因为内核使用数字来代替   

39. 任务控制  Job control

通过不同的按键和命令想发送TSTP(类似STOP)和CONT信号的一种方式  

例如 使用CTRL+Z发送TSTP信号来暂停进程  然后输入fg 将进程置于前台  或 bg 进程移入后台继续运行进程   

jobs 查看暂停了那些进程


* 后台进程 

可以使用 & 将进程设置为后台运行  


40. 文件模式和权限  

读  写   运行   

ls -l  查看权限  

用户权限   用户组权限  其他用户 

\- 代表常规文件

d 代表目录

权限信息:  

r 可读  4

w 可写  2

x 运行  1

\- 无  

有些可执行文件的执行位是s 而不是x 表示你将以拥有者的身份运行该文件 而不是你自己的身份  

* 更改权限   

chmod g+r file  给用户组加可读权限  

chmod o+r file  给其他用户家可读权限  

也可以 chmod go+r 给用户组和其他用户添加可读权限   

chmod go+r 给用户组和其他用户取消K可读权限   

41. 符号链接 

符号链接是指向文件或者目录的文件  

符号链接相当于文件别名 或者windows中快捷方式  

符号链接的文件类型是l  

* 创建符号链接  

ln -s target linkname  

-s 表示是一个符号链接

如果没有 -s选项 ln命令会创建一个硬链接 为文件创建一个新的名字 新文件拥有老文件的所有状态信息  

和符号链接一样 打开这个新文件会直接打开文件内容  

42. 归档和压缩文件  

gzip 命令 只能压缩一个文件  生成一个后缀名gz的文件

gunzip 解压缩gz文件  

tar 压缩多个文件  生成后缀名tar 

tar cvf archive.tar file1 file2 ...

c 代表创建文件  

v 显示详细的命令执行消息  

f 代表文件 后面需要指定一个归档文件名  如果不指定归档文件名 则归档到磁带设备 如果文件名为- 则是归档到标准输入或输出  


解压缩tar文件  

tar xvf archive.tar

x 代表解压缩模式  

内容预览模式

tar t archive.tar  


压缩归档文件 .tar.gz

gunzip  file.tar.gz

tar xvf file.tar  

=== tar ztvf  file.tar.gz

=== zcat file.tar.gz | tar xvf - 

.tgz  === .tar.gz  .tgz是针对MS-DOS的FAT文件系统  


压缩命令 bzip2 生成后缀名为 .bz2文件  

该命令执行效率比gzip稍慢 主要用来压缩文本文件 因而在压缩源代码文件时候比较常用  

相应的解压缩命令 bunzip2  



### 43. Linux目录结构基础  

* bin

存放的是可执行文件  大部分基础Unix命令 如ls cp  

该目录中的大部分是c编译器创建的二进制文件 还有一些现代系统的shell文件  

* sbin

可执行的系统文件 这些可执行文件用来管理系统 普通用户一般不需要使用  

许多命令只有由root用户运行  

------------

* dev

设备文件  

* etc  

配置文件  

* home

home目录

* usr

存放着linux系统文件  

* lib

存放代码库 静态库和共享库  

* tmp  

临时文件

* var

程序存放运行时信息地方  系统日志 用户信息 缓存  

var/tmp与/tmp不同是: 系统不会在启动时清空它


* sys

设备和系统信息  

* proc

包含了当前正在运行的进程的信息以及一些内核参数  

------------

0. root

boot 内核加载文件  

media  加载可移除的地方  

opt  第三方软件  

------------

1. usr/

* bin

* sbin

* man

* lib

* local

* share

------------

2. var/

* log

* tmp

------------


44. 内核位置  

Linux系统的内核通常在/vmlinuz或者/boot/vmlinuz中。  

系统启动时, 引导装载程序将这个文件加载到内存并运行  


45. 超级用户命令 su

/etc/sudoers文件中指定特定用户以超级用户的身份运行命令   

User_Alias ADMINS = user1, user2  为user1 user2 指定了一个别名 ADMINS  

ADMINS ALL = NOPASSWD: ALL  表示别名ADMINS的用户可以运行sudo命令  

冒号:之前ALL表示允许在任何主机运行命令 冒号:之后ALL代表允许执行任何命令  

root ALL=(ALL) ALL  表示root用户在任何主机上执行任何命令   


------------

##### 2 设备管理

1. 设备文件   存在/dev目录中 

b block 块设备  硬盘  


c character 字符设备  

字符设备处理数据流 只能对字符设备读取和写入字符数据  

字符设备没有固定容量  

当你对字符设备进行读写时，内核相对应的设备进行读写操作  

内核在流数据送达设备和进程后不会备份和再次验证  


p pipe 管道设备 

管道设备与字符设备类似 不同的是输入输出端不是内核驱动程序 而是另外一个进程  



s socket 套接字设备   

是跨进程通信经常用到的特殊接口  会存放于/dev目录中  

套接字代表Unix域套接字  

设备用两个数字代表一个设备 一个主要设备号  一个次要设备号   

是内核用来识别设备的数字  相同类型的设备一般有相同的主设备号  

 

2. 访问文件和目录系统 sys

/sys

/sys下有几个目录快捷方式 不过是符号链接

查看实际路径 运行命令： ls -l /sys/block


3. dd命令
对于块设备和字符设备非常有用  
主要功能是从输入文件和输入流读取数据然后写入输出文件和输出流

```
dd if=/dev/zero of=new_file bs=1024 count=1

if 代表输入文件 默认是标准输入

of 代表输出文件 默认是标准输出


bs block size 单位是字节

count=num 代表复制块总数num

skip=num 代表跳过前面的num块

```

4. 设备总结

使用udevadm命令来查询udevd  

在/sys目录下查找设备  

dmesg命令的输出或者内核系统日志中查获设备名  

对系统已经找到的硬盘设备 可以使用mount命令   

运行cat /proc/devices命令  可查看系统为之配备了驱动程序的块和字符设备

5. 硬盘 /dev/sd*
硬盘设备都已sd为前缀来命名  sd代表SCSI disk 小型计算机系统接口 small computer system interface 简称 SCSI  

SCSI最初作为设备之间通信的硬件协议标准而开发  
usb存储设备就是使用SCSI协议通信  

6. 查看SCSI的命令  lsscsi

1' 系统中地址  2‘ 设备的描述信息  3’ 设备文件的路径

Linux按照设备驱动程序检测到设备的顺序来分配设备文件 

现代Linux使用 通用唯一标识符 UUID Universally  Unique IDentifier

7. CD/DVD  /dev/sr*

可读     /dev/sr*   

可读写   /dev/sg*  g代表generic  

8. PATH硬盘  /dev/hd*

9. 终端设备 /dev/tty*   /dev/pts/*   /dev/tty

终端设备负责用户进程和输入输出之间传送字符  

伪终端设备 模拟 终端设备功能 由内核为程序提供IO接口 不是真实的IO设备  例如shell窗口就是伪终端   

常见的两个终端设备是/dev/tty1 第一虚拟控制台  /dev/pts/0 第一虚拟终端   

/dev/tty代表当前进程正在使用的终端设备

Linux系统有两种显示模式: 文本模式  和  X windows系统服务器

Linux系统支持虚拟控制台来实现多个终端的显示

10. 串性端口  /dev/ttyS* 

对应COM1端口

而USB串行适配器在USB和ACM模式下分别表示为 /dev/ttyUSB*   /dev/ttyACM*

11. 并行端口 /dev/lp*

单向并行端口设备   /dev/lp*

双向并行端口设备   /dev/parprot*

12. 音频设备

Linux系统有两组音频设备: 高级Linux声音结构 Advanced Linux Sound Architecture 简称ALSA  
和 开发声音系统 Open Sound System 简称 OSS

13. 创建设备文件 mknod

```
mknod /dev/sda1 b 8 2

b 块设备

8 主编号

2 次编号

c 字符设备

p 管道设备

```

14. 创建设备组  MAKEDEV




------------

##### 3 **硬盘与文件系统**

主引导录 Master Boot Record 简称 MBR

全局唯一标识符区分表  Globally Unique Identifier Partition Table 

分区工具:  
parted        文本工具
gparted       parted图形工具
fdisk         不支持GRT  
gdisk         支持GRT 不支持MBR  

1. 查看区分表  

parted -l

2. 文件系统类型  

ext4   ext3   ext2   

ISO-9660 一个CD-ROM标准  

FAT文件系统     微软   MDOS  

HFS+  苹果  

3. 创建分区

mkfs -t ext4 /dev/sdf2

4. 挂载文件系统 mounting

系统启动时候  内核根据配置信息挂载root目录  



##### 8 **进程与资源利用详解**

计算机硬件资源主要有三种 cpu  内存   IO  

进程为获得这些资源相互竞争 内核负责公平分配资源  

内核本身也是一种软件资源 进程通过它来创建新的进程  并和其他进程通信

1. ps  列出当前运行的进程

2. top 将系统中最活跃的进程

空格   立即更新显示内容
m     内存
t     cpu累计使用量
p     cpu当前使用量
u     仅显示某位用户的进程
f     选择不同的统计信息来显示

3. lsof 查看打开文件

lsof /usr  显示/usr目录下所有打开的文件

lsof -p pid  

4. 跟踪程序执行和系统调用

strace 系统调用跟踪  

ltrace 库调用跟踪

$ strace cat /dev/null  

如果一个进程想启动另一个进程 该进程会调用fork()系统调用来  
从自身创建出一个副本 然后副本调用exec()系统调用来启动和运行新的程序  

5. 线程 thread

线程ID: TID  

线程与进程的区别：
进程之间不共享内存和IO这样的系统资源  

同一个进程中的所有进程则共享该进程占用的系统资源和一些内存  


单线程进程 与  多线程进程

所有进程最开始都是单线程  其实线程通常称为主线程  
主线程随后可能会启动新的线程 这样进程就变为多线程  
这个过程与进程使用fork()创建新进程类似  

并发又有伪并发和真并发，伪并发是指单核处理器的并发，真并发是指多核处理器的并发


6. 查看线程

ps  m  

ps  m  -o pid,tid,command

7. 资源监控

* 测量CPU时间

time ls

user 用户时间  CPU用来运行程序代码的事件  单位秒

system 系统时间  内核用来执行进程任务的时间   

elapsed 消耗时间  指进程从开始到结束所用的时间

* 调整进程优先级

pr 优先级   数值越小 优先级越高

NT 字段  默认值 0  显示有关内核进程调度的少许信息
如果你想干预内核的进程调度 这个信息会对你有用   内核使用NT值来决定进程下一次在什么时间运行  

renice 20 pid

* 平均负载

平均负载 load average 是准备就绪待执行的进程的平均数  

也就是某一个刻可以使用CPU的进程数的一个估计值

如果平均负载值接近1 说明某个进程可能完全占用了CPU  

现在很多系统是多个处理器核心cpu 这是进程可以同时运行  

如果有两个核心并且平均负载为1 这意味着只有其中一个处于活跃状态

如果平均负载为2 说明两个CPU都处于忙状态


8. uptime使用

显示三个平均负载值和内核已经运行的时长  

9. 高负载

系统如果有足够的内存和IO资源可以运行许多进程  

如果平均负载值高 同时系统响应速度还很快 这说明系统中有很多  
进行在共享CPU 进程间需要相互竞争CPU时间 如果它们放任其他进程长时间占有  
它们自身的运行时间就会大大增长  对于Web服务器来说  高平均负载值也是正常现象  
因为进程启动和结束很快 以至于平均负载检测机制无法获得有效的数据  

如果平均负载值高并且系统响应速度很慢的话 可能意味着内存性能问题  


10. 内存

查看系统内存命令  free  

* 内存工作原理  

CPU通过MMU 内存管理单元 将进程使用的虚拟的地址转换为实际的内存地址  
内核帮助MMU把进程使用的内存划分分为更小的区域 称为页面  

内核负责维护一个数据结构 称为页面表  

其中包含从虚拟页面地址到实际内存地址的映射关系  

当进程访问内存是 MMU根据此表将进程使用的虚拟地址转换为实际的内存地址

进程执行时并不需要立即加载它所有的内存页面 内核通常在进程需要的时候加载和分配内存页面  
称为按需内存分页  on-demand-paging  demand paging  

原理：  
1' 内核将程序指令代码的开始部分加载到内存页面内  
2' 内核可能还会为新进程分配一些内存页面供其运行使用  
3’ 进程执行过程中  可能代码的下一个指令在已加载的内存页面中不存在 这时内核接管控制  
加载需要的内存页面 然后让程序恢复运行  

4' 如果进程需要更多的内存 内核接管控制  并且获得空闲的内存空间分配给进程  


内存错误分为： 轻微   严重  

严重内存页面错误发生在进程需要的内存页面在主内存不存在时 意味着内核需要从磁盘  
或者其他低速存储媒介中加载  

* 查看内存页面错误  ps top time


11. 使用vmstat监控CPU和内存性能  

vmstat 是旧命令 但是运行开销小  

12. IO监控  iostat

13. 查看进程的IO使用和监控  iotop

TID  线程ID   

PID  进程ID  

PRIO 优先级  

be best-effort 尽力  
内核尽最大努力为其公平的调度IO  
大部分进程是在这类IO调度等级下运行  

rt real-time   实时  
内核优先调度实时IO  

idle           空闲  
内核只在没有其他IO工作的时候安排此类IO工作  

* 查看和更改进程IO优先级 使用 ionice  

* 使用pidstat 监控进程

查看进程在某一个时间段内资源使用情况  
默认情况下显示用户时间和系统时间的百分比  以及综合的cup时间百分比  
显示进程在哪一个CPU上运行  

guest字段指进程在虚拟机上运行时间的百分比


14. 网络配置

* 查看IP地址  ifconfig

* 子网

子网是一组相互连接的 带有按序排列的IP地址的主机  

划分子网需要考虑两点：

1' 一个网络前缀

2' 一个子网掩码  

* 无类域内路由选择 Classless Inter-domain routing---CIDR

写成 10.23.2.0/24 

* route -n 命令显示路由表

* 默认网关  

0.0.0.0/0能匹配互联网中的所有IP 它是默认路由 其Gateway列的地址是默认网关

* ICMP 网际控制报文协议  internet control message protocol

用于查找路由和链接问题


* DNS 域名系统 domain name service  

使用名称代替IP地址   

* ping host/dns发送一个ICMP请求报文给一台主机 

* traceroute  host

显示数据包达到目标主机所走过的路

* host dns

找出域名的IP地址

host也可以反查IP地址的域名
但是因为不同的域名可以指向同一个IP地址  
所以DNS无法得知那个域名才是你要的  

* MAC地址

介质访问控制 media access control  硬件地址

* 手动设置IP地址和掩码

ifconfig  interface  ipaddress netmask mask

注意netmask是关键字 

* 设置网关

route add default gw gateway-address  


route del -net default 删除网关  

* DHCP 动态主机配置协议

Dynamic Host Configuration Protocol   

* NetworkManager的操作  

NetworkManager是一个开机时系统就启动的守护进程  
它的任务是监听系统和用户的事件 并根据一堆规则来改变网络配置  

NetworkManager配置文件是放在/etc/NetworkManager目录中  
通常是NetworkManager.conf  

* 解析主机名

查找规则在/etc/nsswitch.conf文件中  
通常规则是/etc/hosts的规则 然后使用DNS  


* resolv.conf 指定DNS服务器

* 缓存和零配置DNS

常见的守护进程: dnsmasq  和   nscd  

dnsmasq 默认配置文件是 /etc/dnsmasq.conf  

多播dns--mDNS 和 简单服务发现协议 Simple Service Discovery Protocol


* localhost

ifconfig里面有一个lo 是虚拟网络接口 叫作环回 loopback 连接 127.0.0.0.1 其实就是本机  当发送数据到内核网络接口lo  
内核只会将其重新包装 并通过lo回复  

开机脚本所用到静态网络配置一般都是lo环回接口   

* Tcp端口与连接

TCP方式是指不同的网络应用使用不同的网络接口  

使用TCP时  应用会在本机的一个端口和远程机器的一个端口之间建立连接  

一个链接可以用’IP加端口‘代码来标识  

* 查看网络链接 netstate   
-n 表示不用对主机名进行DNS解析  
-t 只输出TCP链接

* 端口与/etc/services

Linux中只有超级用户可以使用1-1023端口

1024-65535端口

* UDP 使用NTP 网络时间协议  Network Time Protocol


* 理解DHCP

DHCP 自动获取网路配置 就是让主机使用动态主机配置协议 Dynmaic Host Configuration protocol 来获取IP  子网掩码  默认网关  DNS服务器


主机必须先能发现至网络中的DHCP服务器 才能通过DHCP获取网络配置  因为每个物理网络必须有自己的DHCP服务器 在普通网络中 路由器充当DHCP服务器  

第一次发送请求不知道DHCP服务器 所以是广播方式  

* dhclient 

* Linux配置路由器 

路由器看成是一种不止一个网络接口的计算机  

* 激活路由器内核IP转发 sysctl -w net.ipv4.ip_forward

* 私有网络  

10.0.0.0/8

192.168.0.0/16

172.16.0.0/12

私有网络对互联网来说是不可见的 而互联网也不会终结发数据包到私有网络里  

* 网络地址转换NAT

* 防火墙

IP过滤  

Linux 防火墙的队则是链型 一个表就是一套链 数据包在Linux网络子系统的各部分移动时  内核就会对包应用某套规则

从物理层接收到一个新的包之后 内核就会根据输入的数据激活对应的规则  

这些数据结构由内核来维护  整个系统叫iptables 

可以用iptables命令创建和修改规则  

现在用一个叫nftables 来代替 iptables

filter表

该表里面有三个基本的链  
Input output   forward

* 设置防火墙的规则

iptables -L


* 以太网  IP ARP

ARP 地址解析协议 address resoltion protocol  

维护ARP表  包含了IP地址与MAC地址映射关系  

Linux中 ARP缓存在内核中 使用命令arp  

ARP只应用于本地子网 至于子网以外的地方 主机只会将数据包发给路由器  

主机还是要知道路由器的MAC地址 而这也是用ARP来获取  


ARP      IP地址      ---->      MAC地址

RARP     MAC地址     ---->      IP地址

RARP reverse address resolution protocol  反向地址协议


15. 网络应用与服务

* telnet 

telnet host port

telnet 原本是用于登录远程主机  

telnet客户端则是一个调试远程服务器服务的有用工具  

telnet 只支持TCP 

* 多用途网络客户端 netcat

* http协议  curl

curl -trace url  

* 网络服务器

httpd apache apache2   web服务器  

sshd   secure shell守护进程  

postfix  qmail sendmail  邮件服务器  

cupsd   打印服务器  

nfsd  mountd 网络文件系统守护进程 文件共享  

smbd  nmbd   文件共享守护进程  

rpcbind 远程程序调用RPC端口映射服务守护进程  

* sshd服务器

运行sshd需要一个配置文件和主机密钥   

文件放在/etc/ssh   

主机密钥 一个公钥 扩展名 pub  一个私钥 无扩展名  

* ssh-keygen 生成命令  

* 开启SSH服务器  

chkconfig sshd on  检查ssh是否开启  

* ssh客户端  

ssh remoteusername@host

* 守护进程inted xinted

用于网络


* 诊断工作

netstat 命令

-t  tcp

-u  udp

-l  打印监听中的端口  

-a  打印活动中的端口  

-n  取消域名查找   

* lsof 能够跟踪打开的文件  还可以列出正在使用或监听端口的程序  

lsof -n -i 取消DNS查找   

lsof -i:port 寻找特定端口  

lsof iprotocol@host:prot
lsof ipTCP:80

* tcpdump 

* netstat 网络

* 扫描端口 网络映射器 Network Mapper 简称Nmap

扫描并列举出一台机器(一个网络)的开放端口 

* 对一个主机进行端口扫描 

nmap host   

* 远程程序调用  

RPC 远程程序调用 remote procedure call  
使本地程序调用远程程序(按程序号来标识) 让远程程序返回结果码或信息  

RPC的实现需要用到传输层协议 它需要一种特别的中介服务来将程序号与TCP和UDP的端口号对应起来   
这种服务就是rpcbind 运行RPC服务就需要用到它:  

想看计算机运行什么RPC服务:

rpcinfo -p localhost  

* 典型漏洞 

直接攻击   嗅探明文密码  

* 套接字 进程与网络通信方式

在Unix上 进程是套接字来标识与网络通信的时机与方式的  

套接字是进程通过内核访问网络的季候 它代表用户空间和内核空间的边界  
它也常被用于进程间通信 Interprocess Commmunication 简称 IPC

因为进程需要以不同的方式访问网络  所以套接字也分不同种类  

TCP连接会用流式套接字    sock_stream 

UPD连接则用数据包套接字  scok_dgram  

套接字： 监听套接字  和    读写套接字   

主进程用监听套接字在网络中寻找连接  

当一个新的连接到来  主进程就用accept()系统滴啊用来接收该连接  

它能为连接创建专用的读写套接字  接着主进程用fork()创建一个新的子进程来处理该连接   

最终监听套接字继续监听 为主进程带来更多连接   

* Unix域套接字 

同一台机器上的进程则使用IPC来协商有哪些工作要做 以及由谁来负责  


16. shell脚本

* #! /bin/sh  表示要用/bin/sh程序来执行脚本文件找那个的命令


\#! 叫shebang

以#开头 表示注释  


为了能运行 chmod +rx script  


* 引号与字面量 

字面量  

引号通常用于创建字面量 即原封不动的字面意

* shell如何执行一条命令  

1' 在执行之前 shell会查找其中的变量 通配符以及其他代词 如果有的话 就将他们进行替代  

2' 将替换后的结果返回给命令

* 单引号  

单引号中内容 shell不会进行替换  

* 双引号  

双引号中的所有变量都进行扩展

* 转义

通过反斜杠进行转义  

* 特殊变量  

$1...$n 脚本的参数值

shell的内置命令shift能删除第一个参数$1  

* $#  持有传给脚本的变量的数量

* $@  代表脚本接收的所有参数  

如果脚本中有一行的长度超出文本编辑器的范围 用反斜杠 \  

* $0 脚本名  

* $$ 进程号  

* $? 退出码  

$?变量持有shell脚本执行的最后一条命令的退出码  

* 退出码 

Unix程序退出时  会留一个退出码给启动该程序的父进程   
它是一个数字 有时也叫错误码 或 退出值  

当退出码是0时 通常表示程序运行正常  

如果是非正常结束 那么退出码通常会是非零数 

当需要让脚本非正常退出时 可使用exit 1 指定退出码1 并传给父进程

ps: 有些程序如diff grep正常退出时也会使用非零的退出码  


16. 用户环境

* dot文件

以点开头的文件  是在某些程序初次启动时产生  


创建启动文件的规则  简单  可读

* 命令路径 

shell启动文件中最重要是命令路径  
该路径应包含用户要用到的所有应用程序所在的目录  

至少 按照顺序应包含这些:  

/usr/local/bin  
/usr/bin
/bin

这个顺序可确保能使用/usr/local中的特定变体来覆盖默认程序  

大多数Linux发行版中 几乎所有软件包都会被安装到/usr/bin中  

为了管理软件 自己安装软件后 将软件的符号链接到/usr/local/bin中  


新的：

$home/bin  

$home/.local/bin  

如果向使用系统工具可加上sbin目录  

/usr/local/sbin  
/usr/sbin  
/sbin  

* 提示符

PS1 = '\U\$'  

\U 用于代替当前用户
\$ 命令符
\h 主机名
\w 当前目录

\$ 普通用户为$  root用户为#  

* 权限掩码

umask   

* bash shell

应该使用.bashrc 并建立名为.bash_profile的符号链接指向它  

1' 登录shell  

通过/bin/login之类的程序登录系统时所获得shell  

用SSH来远程登录 获取的也是登录shell   

当bash以登录shell形式运行时  会运行/etc/profile  
然后就会寻找用户的.bash_profile .bash_login .profile文件   
并运行所找到的第一个文件


2' 非登录shell  

当bash以非登录shell启动时  会运行/etc/bash.bashrc 以及用户的.bashrc   



17. linux的C

1. 编译器 gcc

yum install gcc  

brew install gcc  


2. 多个源码文件  


使用编译器的 -c 选项来给文件生成对应的对象文件  

对象文件是一种二进制文件  

操作系统是不知道如何运行对象文件的  

你可能会需要将一些对象文件和系统库组合成一个完整的程序  

要从一个或多个对象文件建立一个功能完整的可执行程序  需要用连接器完成这个步骤  

生成对象文件：

cc -c main.c

cc -c aux.c

生成main.o 对象   

链接命令  unix的ld 

cc -o main main.o aux.o

将main对象文件和aux对象文件连接成可执行文件main

3. c预处理器 

c preprocessor 简称cpp  

预处理器会将源码重写成一种编译器能理解的形式   

源码中的预处理器命令叫作指令 directive 以#开头   

分为以下三种:  

include文件  #include指令会使预处理器将整个文件包含进来  

宏定义 # define  BLAH  something  会使预处理器将源码中所有BLAH替换成something  

宏名字一般都是大写   

条件  #ifdef   #if #endif 

\#ifdef MACRO指令用于检查宏MACRO是否定义  

\#if  condition 则检查condition是否非零

4. 连接库

所谓c库 就是一些已编译好的 通用的 可让你添加到自己程序的函数  

库主要在连接的时候(即连接器从对象文件产生可执行程序之时)发挥作用   

库分布在lib目录中 默认是/usr/lib  

5. 共享库

以.a结尾的库 是静态库  

当程序连接的是静态库时  连接器会将库文件中的机器码复制到你的程序中  
最终的可执行程序不需要该库也能运行起来  并且其所引用到的行为将不会改变   

6. 共享库

以.so结尾的库 是共享库 

引用共享库的程序只会在需要时才将该库加载到内存中 而且多个进程可以共享内存中的同一个共享库  

修改共享库的代码不需要重编译那些引用的它的程序   

7. 共享库注意点  

如何列出程序需要的共享库  

ldd  progname   // ldd  程序名

程序如何检查共享库  

如何让程序连接共享库   


8. 列出共享库的依赖关系  

共享库和静态库通常放在一个地方  Linux的两个标准库目录是/lib和/usr/lib中

其中/lib是不应该包含静态库  


9. ld.so 怎样找到共享库  


如果可执行程序有预先配置好的运行时库搜索路径 runtime libary search path  检查rpath   

则动态连接器一般会首先查找哪里   

动态连接器就会参考系统缓存 /etc/ld.so/cache   

这是从缓存配置文件 /etc/ld.so.conf  


10. 脚本语言

脚本第一行

\#! 语言解释器路径

例如:

\#! /usr/bin/python   

或  

\#! /usr/bin/env python  

 
11. 从C代码编程出软件  

从C代码安装一个软件 通常的步骤:  

1' 将源代码的归档解包   

2’ 对包进行设置  

3’ 运行make来构建程序  

4' 运行make install 或者发行版特定的安装命令来安装该包   

* GNU autoconf 

用于自动产生Makefile系统  

使用此系统的包都会带有configure Makefile.in config.h.in 

.in 文件是模板   

运行configure脚本来分析你系统特性  

然后在.in文件的基础上替换 最后创建出真正的构建文件   

运行 ./config 就能从Makefile.in 产生出Makefile 


































 







 









