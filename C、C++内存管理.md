##### C内存分布：

sizeof:求类型/对象的大小（尤其是字符串数组，数组最后一定要加上‘\0’）

strlen:有效字符长度（遇到‘\0’结束）

指针的大小是4/8个字节，指针是地址，本质是对内存字节的编号（地址）。

32位的程序      虚拟进程地址空间总计有42亿9千万个字节  从32个0到32个1    

64位的程序

空指针是存在的，是进程空间地址的第一个字节。

32位/64位 --->虚拟进程地址空间--->进程--->编译器--->人



内核空间

栈（向下增长）

内存映射段（文件映射、动态区、匿名映射）

堆（向上增长）

数据段（静态区）：（全局数据、静态数据）  从操作系统角度来说是数据段，从C语言角度来说是静态区

代码段（常量）：（可执行代码、只读常量）   存的代码是编译出来的指令（机器码），我们写的代码存在磁盘



面试题：

1.为什么要建立虚拟的这种映射？

2.在32位下，最大能malloc多大的堆？如何malloc出来3g或者更大的空间？

答：最大能malloc出不超过3g的内存空间，把32位改成64位。

3.malloc、calloc、realloc的区别？如何判断realloc出来的空间是原来空间上再加上所需空间，还是重新找的一段空间？

int*p1=(int *)malloc(4);

int* p2=(int*)realloc(p2,12);

只需判断p1、p2的地址是不是同块内存空间即可。



malloc：申请空间

calloc：申请的空间初始值都是0

realloc：调整空间



##### C++内存管理方式：

int* ptr4 = new int;               delete ptr4;

int* ptr5 = new int (10);       delete ptr5;

int* ptr6 = new int [3];         delete[] ptr6;

内置类型中， malloc和new效果一样。delete和free效果一样。

Test* p3 = new Test;

Test* p4 =(Test*)malloc(sizeof(Test));

Test* p5 = new Test[10];

自定义类型中，new除了开辟空间还调用自定义类型的构造函数进行初始化

​                           delete先进行析构函数，再释放空间。而free只有释放

##### operator new与operator delete函数

operator new   ==   malloc   +   失败抛异常nomeme

operator delete   ==  free



###### 内置类型：

new  int ==》    operator new 开空间/抛异常==  malloc+抛异常      ==》构造

malloc int 

###### 自定义类型：

new Test  ==》   operator new开空间/抛异常 == malloc+抛异常      ==》构造

malloc Test 



###### 内置类型：

delete int   /   free int

###### 自定义类型：

delete Test ==》析构 ==》operator delete

free Test



new T[N]的原理

1.调用operator new[]函数，在operator new[]中实际调用operator new函数完成N个对象空间的申请

2.在申请的空间上执行N次构造函数



delete[]的原理

1.在释放的对象空间上执行N次析构函数，完成N个对象中资源的清理

2.调用operator delete[]释放空间，实际在operator delete[]中调用operator delete来释放空间



可以对全局的operator new 和operator delete进行函数重载，来实现别的功能，例如链表节点使用内存池申请和释放内存，提高效率

##### 定位new表达式：

定位new表达式是在已分配的原始内存空间中调用构造函数初始化一个对象。

Test* pt=(Test*)malloc(sizeof(Test));            

new(pt)Test;    //new(pt)Test(100);



Test* pt =(Test*)operator new(sizeof(Test));   new(pt)Test;<====>Test * pt = new Test;(等价关系)

 operator new  +  定位new表达式     ==   new

池化技术

频繁地创建线程----》线程池

频繁地连接数据库---》连接池

频繁地申请释放内存---》内存池：减少内存碎片、提高效率



##### 常见面试题

###### malloc/free  和 new/delete的比较：

公共点：都是从堆上申请空间，并且需要用户手动释放

区别：

1.malloc和free是函数，new和delete是操作符

2.malloc申请出来的空间不会初始化，new可以初始化

3.malloc申请空间时，需要手动计算空间大小并传递，new只需在其后跟上空间的类型即可

4.malloc的返回值为void*，在使用时必须强转，new不需要，因为new后跟的是空间的类型

5.malloc申请空间失败时，返回的是NULL，因此使用时必须判空，new不需要，但是new需要捕获异常

6.申请自定义类型对象时，malloc/free只会开辟空间，不会调用构造函数与析构函数，而new在申请空间后会调用构造函数完成对象的初始化，delete在释放空间前会调用析构函数完成空间中资源的清理。

7.new/delete比malloc和free的效率低点，因为new/delete的底层封装了malloc/free

new出来的空间一定在堆上吗？

一次new操作分配到的一块内存是连续的，而两次new操作分配到的两块内存可能就不在一起了。

###### 类的对象的建立：

在C++中，类的对象建立分为两种，一种是静态建立，如A a；另一种是动态建立，如A* ptr=new A；这两种方式是有区别的。

静态建立一个类对象，是由编译器为对象在栈空间中分配内存，是通过直接移动栈顶指针，挪出适当的空间，然后在这片内存空间上调用构造函数形成一个栈对象。使用这种方法，直接调用类的构造函数；
动态建立类对象，是使用new运算符将对象建立在堆空间中。这个过程分为两步，第一步是执行operator new()函数，在堆空间中搜索合适的内存并进行分配；第二步是调用构造函数构造对象，初始化这片内存空间。这种方法，间接调用类的构造函数。

匿名对象

设计一个类，该类只能在堆上创建对象

设计一个类，该类只能在栈上创建对象

##### 设计模式

###### 单例模式：

1.饿汉模式

2.懒汉模式    （加锁/解锁    线程安全  双检查double check）

##### 内存泄漏

内存泄漏指因为疏忽或错误造成程序未能释放已经不再使用的内存的情况。内存泄漏并不是指内存在物理上的消失，而是应用程序分配某段内存后，因为设计错误，失去了对该段内存的控制，因而造成了内存的浪费。

堆内存泄漏

系统资源泄漏

解决方案：

1.事前预防型：如智能指针等  

2.事后查错型：如泄漏检测工具

