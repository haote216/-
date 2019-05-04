### 基础IO



fopen、fclose、fread、fwrite都是C标准库当中的函数，我们称之为库函数。

而open、close、read、write、lseek都属于系统提供的接口，称之为系统调用接口。

回忆一下，讲操作系统时的图，系统调用接口和库函数的关系，一目了然。

f#系列的函数，都是对系统调用的封装，方便二次开发。

fopen：

fwrite：

FILE：

fp：

fread：

EOF：只能判定文本文件的结束

feof：基本上可以判定所有文件的结束          	//根据属性

```
输出信息到显示器
#include<stdio.h>
#include<string.h>
int main()
{
    const char *msg ="hello fwrite\n";
    fwrite(msg,strlen(msg),1,stdout);
    printf("hello printf\n");
    fprintf(stdout,"hello fprintf\n");
    return 0;
}
//fwrite
//printf
//fprintf
```

stdin、stdout、stderr
FILE *：

#### 系统文件I/O

```
open：
	int open(const char *pathname,int flags);
	int open(const char *pathname,int flags,mode_t mode);
	
pathname:要打开或创建的目标文件
flags：打开文件时，可以传入多个参数选项，用下面的一个或者多个常量进行“或“运算，构成flags。 在定义时，是int flags，整形数字，所以这些大写的字母都是宏，定义的是整形数字。
	O_WRONLY：只读打开
    O_RDONLY：只写打开
    O_RDWR：读写打开
    O_CREAT：若文件不存在，则创建它。需要使用mode选项，用来指明新文件的访问权限
    O_APPEND：追加写
mode_t mode：自行创建的文件的权限。（八进制）

返回值：
	成功：新打开的文件描述符
	失败：-1
```

```
write：
	write(fd,msg,len);   //fd：   msg：缓冲区首地址   len：本次读取，期望写入多少个字节的数据   返回值：实际写了多少字节数据
```

```
read：
	ssize_t read(int fd,void *buf,size_t count);
read会把参数fd所指的文件传送count个字节到buf指针所指的内存。
返回值：实际读取到的字节数。
	如果返回0，表示已到达文件尾或无可读取的数据。
	失败：-1，并将根据不同的错误原因适当的设置错误码。
```

##### 文件描述符fd

文件描述符就是从0开始的小整数。当我们打开文件时，操作系统在内存中要创建相应的数据结构来描述目标文件。于是有了files_struct结构体，表示一个已经打开的文件对象。而进程执行open系统调用，所以必须让进程和文件关联起来。每个进程都有一个指针*file，指向一张表files_struct，该表最重要的部分就是包含一个指针数组，每个元素都是一个指向打开文件的指针。所以，本质上，文件描述符就是该数组的下标。所以拿着文件描述符，就可以找到对应的文件。

###### 0 & 1 & 2

​	Linux进程默认情况下会有3个缺省打开的文件描述符，分别是标准输入0，标准输出1，标准错误2

​	0,1,2对应的物理设备一般是：键盘、显示器、显示器。

文件描述符的分配规则：在file_struct数组当中，找到当前没有被使用的最小的一个下标，作为新的文件描述符。

```c
int main()
{
    close(1);
    int fd = open("myfile.txt",O_WRONLY | O_CREAT,0644);
    if(fd < 0){
        perror("open error");
        return 1;
    }
    int i = 0;
    while(i<10)
    {
        i++;
   		printf("hello bit\n");
   		fflush(stdout); 
    }
    close(fd);
    exit(0);
}
```

结果：本来应该输出到显示器的内容，输出到了文件myfile当中，其中，fd=1.这种现象叫做输出重定向。

重定向的本质：在file_struct这个表中，myfile文件的文件描述符应该是3。由于关闭了下标为1的标准输出，所以myfile文件的文件描述符找到了1这个没有被使用的下标，所以往1号文件描述符写的内容，都被写到了myfile当中，而不再写到标准输出。printf是C库的IO函数，一般往stdout中输出，但是stdout底层访问文件的时候，找到还是fd：1，此时，fd：1下标所表示内容，已经变成myfile的地址，不再是显示器文件的地址，所以，输出的任何消息都会往文件中写入，进而完成输出重定向。

追加重定向：open打开函数时用上O_APPEND。

```
close(1);
int fd = open("myfile.txt",O_WRONLY | O_APPEND);//    一定要加上O_WRONLY
```

##### FILE

write:系统接口          printf、fwrite:库接口

```
三种缓冲方式：
	无缓冲：不缓冲，直接刷新
	行缓冲：按行进行刷新
	全缓冲：只有把缓冲区写满的时候才刷新。除非强制刷新
如果文件是显示器，默认是行缓冲。如果文件是普通文件，默认是全缓冲。
重定向：把本来该输出到显示器的东西输出到了文件。刷新方式由行缓冲变成了全缓冲。
```

我们这里所讨论的缓冲区是用户级缓冲区，是由C标准库提供的。OS也会提供相应内核级缓冲区，这里我们不再讨论。

这个缓冲区的位置就是在FILE* 这个结构体里面的。

##### 理解文件系统

文件分俩部分：

1.属性信息：ls-l

2.内容信息：cat、more、less

ls -li ：打印出当前文件inode号

stat 文件名：查寻文件的属性信息

Access time:文件最后访问时间
Modify time:对文件内容信息最后修改时间
Change time:对文件属性信息最后修改时间

inode位图、数据位图     位图（比特位的位置代表的是对应的数据块是否被占用）

超级块：存放文件系统本身的结构信息

i节点表：存放文件属性（文件大小、所有者、最近修改时间等）

数据区：存放文件内容

目录存的是文件名和inode的映射关系

在一个目录下创建一个文件需要写权限：因为创建一个文件需要在该目录对应的数据部分写入文件名和inode的对应关系。

显示一个目录下所有文件需要读权限：因为读取目录下保存的文件名和inode的对应关系。

文件名和inode之间的对应关系将文件名和文件的内容及属性连接起来。

###### 硬链接

ln file file-hard

硬链接和目标文件是同一个inode

###### 软链接

ln -s myfile.txt myfile.txt-soft

软链接是一个独立的文件，有独立的inode。可以理解软链接文件内部放的是指向文件的路径。

创建一个文件的默认硬链接是1，创建一个目录的默认硬链接是2

##### 静态库和动态库

静态库（.a）：程序在编译链接的时候就把库的代码链接到可执行文件中。程序运行的时候将不再需要静态库。

动态库（.so）：程序在执行的时候才去链接动态库的代码，多个程序共享使用库函数。

一个与动态库链接的可执行文件仅仅包含它用到的函数入口地址的一个表，而不是外部函数所在目标文件的整个机器码。

在可执行文件开始运行以前，外部函数的机器码由操作系统从磁盘上的该动态码中复制到内存中，这个过程称为动态链接。

动态库可以在多个程序间共享，所以动态链接使得可执行文件更小，节省了磁盘空间。操作系统采用虚拟内存机制允许物理内存中的一份动态库被要用到该库的所有进程共用，节省了内存和磁盘空间。

```
生成静态库：  gcc -c add.c -o add.o
			gcc -c sub.c -o sub.o
			ar -rc libmymath.a add.o sub.o
```

```
库在当前路径下，用静态库编译：gcc main.c -I./mylib/include -L./mylib/lib -lmymath
```

```
生成动态库：  gcc -fPIC -c sub.c add.c
			gcc -shared -o libmymath.so *.o
```

```
更改环境变量LD_LIBRARY_PATH:export LD_LIBRARY_PATH=./mylib/lib/
库在当前路径下，用静态库编译：gcc main.c -I./mylib/include -L./mylib/lib -lmymath
```

