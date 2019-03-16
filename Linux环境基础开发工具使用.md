#### vim：编辑器

```
vim三种模式
命令模式（command mode）
插入模式（insert mode）
末行模式（last line mode）

vim正常模式命令集：

hjkl：左下上右

yy：复制
 10yy:把光标所在行及其下面行共10行都复制
 yy10:把光标所在行复制10次
 
dd：剪贴
 3dd：把光标所在行及其下面俩行都剪贴

p：粘贴
u：撤销上一步操作
ctrl + r:恢复撤销

shift + ~:切换大小写

x:删除光标右侧的字符
     nx：删除光标右侧指定个数的字符
X：删除光标左侧的字符
    nX:删除光标左侧指定个数的字符

w：按单词向后移动光标
b：按单词向前移动光标

r+替换的字符：把光标处的字符替换成想要替换的字符
   3r+替换的字符：依次同时替换3个字符
R：替换模式
从光标处的字符依次往后被替换

shift + :    :进入末行模式
/main   :从上到下查找main
（或者?main）:表示从下到上查找main
结果出来后再按n：匹配的关键字的下一个

在末行模式下，vs test1.c同时分屏操作当前文件和test1.c

ctrl + w + h:光标移动到左侧的文件
ctrl + w + l:光标移动到右侧的文件
```

#### gcc/g++：编译器

```
gcc进行程序翻译
1.程序的预处理      gcc -E test.c -o test.i
2.编译                     gcc -S test.i -o test.s
3.汇编                     gcc -c test.s -o test.o
4.链接                     gcc test.o -o test
  执行                     ./test

gcc test.c -o test
```

    动态链接：动态链接就是，在生成可执行程序的时候，只是引用的未定义的符号作了标识，到加载到内存中的时候才进行符号重定位。基本思想是把程序按照模块拆分成各个相对独立部分，在程序运行时才将它们链接在一起形成一个完整的程序，而不是像静态链接一样把所有程序模块都链接成一个单独的可执行文件。 假设现在有两个程序program1.o和program2.o，这两者共用同一个库lib.o,假设首先运行程序program1，系统首先加载program1.o，当系统发现program1.o中用到了lib.o，即program1.o依赖于lib.o，那么系统接着加载lib.o，如果program1.o和lib.o还依赖于其他目标文件，则依次全部加载到内存中。当program2运行时，同样的加载program2.o，然后发现program2.o依赖于lib.o，但是此时lib.o已经存在于内存中，这个时候就不再进行重新加载，而是将内存中已经存在的lib.o映射到program2的虚拟地址空间中，从而进行链接（这个链接过程和静态链接类似）形成可执行程序。
    
    动态链接的优缺点：节省空间，效率相对高，对库的依赖性特别强（比如库的版本更新）
    
    静态链接和动态链接两者最大的区别就在于链接的时机不一样，静态链接是在形成可执行程序前，而动态链接的进行则是在程序执行时
```
静态链接：静态链接就是，在生成可执行程序的时候，把目标文件.o 和 静态库 .a ，使用 ld 链接器，链接生成一个可执行程序。这是在程序加载前就完成的动作。

静态链接的优缺点：独立性、可移植性特别强，浪费空间，程序大小相对大，效率相对低
```

```
gcc在进行程序编译时默认链接的方式是动态链接。
ldd：查看文件的链接属性
gcc test.c -o test_static -static：修改test.c文件的链接属性为静态链接。
在Linux下，静态库的后缀为“.a”，动态库的后缀为“.so”
```

#### gdb：调试工具

```
程序的发布方式有两种，debug模式和release模式。Linux gcc/g++编译出来的二进制程序，默认是release模式。要使用gdb调试，必须在源代码生成二进制程序的时候，加上-g。例gcc test.c -o test_debug -g。

gdb test.c：进入gdb的交互式命令行模式

quit：退出

list / l 行号：显示文件代码，接着上次的位置往下列，每次列10行
list / l 函数名：列出某个函数的源代码

b 行号 ：在某一行设置断点
info b ：查看断点
d n：删除序号为n的断点
disable 1：关闭序列号为1的断点
enable 1： 开启序列号为1的断点

c：从当前位置开始连续而非单步执行程序
r：运行程序
n：单条执行
s：进入函数调用内部

p 变量：打印变量值
p &变量：打印变量的地址
until 行号：跳转到行号的有意义行
finish：执行到当前函数返回，然后停下来等待命令

display变量：常显示
undisplay n：取消序列号为n的常显示

set var i=40:修改循环变量i的值为40
bt：查看各级函数调用
```

#### Makefile：自动化构建工程

src：查看源代码



创建一个Makefile文件（依赖关系和依赖方法）

```
hello:hello.c                         //生成的可执行文件：依赖的源文件
	gcc -o hello hello.c              // makefile的依赖方法必须以TAB键开头

.PHONY:clean                          //.PHONY是定义伪目标，伪目标的特点是总是被执行
clean:
	rm -f hello
```

```
hello:hello.o
	gcc hello.o -o hello
hello.o:hello.s
	gcc -c hello.s -o hello.o
hello.s:hello.i
	gcc -S hello.i -o hello.s
hello.i:hello.c
	gcc -E hello.c -o hello.i
	
.PHNOY:clean
clean:
	rm -f hello.i hello.s hello.o hello
```

make

make clean

makefile根据文件的修改时间判定一个源文件是否被修改过，进而决定是否需要被重新编译。

.PHONY起的作用时告诉makefile不要看文件修改时间，直接编译。

makefile带来的好处就是“自动化编译”，写好之后，只需要一个make命令，整个工程就完全自动编译，极大地提高了软件开发的效率。

\n： 换行

\r：  回车

ls > makefile     把ls的结果写到makefile里面去

C函数如printf函数在输出时默认shell将数据输出到标准输出，如显示器。

而printf输出时并不是把消息直接打到显示器上，而是先写至自己的输出缓冲区。

而该缓冲区按行刷新（遇到\n，就把包括\n在内之前的所有字符串全部显示出来）。

fflush:清除读写缓冲区

stdin：终端的标准输入

stdout：终端的标准输出

stderr：终端的标准错误输出

###### Linux下第一个小程序：进度条

```
#include<stdio.h>
#include<unistd.h>
int main()
{
    int i=0;
    char bar[102]={'\0'};
    const  char* lable="|/-\\";
    while(i <= 100)
    {
        bar[i]='#';
        printf("[%-100s][%d%%][%c]\r",bar,i,lable[i%4]);
        fflush(stdout);
        i++;
        usleep(50000);
    }
    printf("\n");
    return 0;
}
```

linux下三种安装方式：源码/rpm/yum

#### yum：软件安装

yum需要处于联网状态才能正常工作

x86_64表示64位系统的安装包，i686表示32位系统的安装包

el7表示的是centos7/redhat7      el6表示centos6/redhat6

sudo yum install gcc.x86_64  安装软件

sudo yum remove gcc.x86_64   卸载软件

