1.linux三个时间：Access time    Modify time   Change time

访问时间，修改时间，状态时间。

2.求文件大小：rewind  malloc以二进制形式读，以二级制形式写

3.sudo      用户切换

init   3  

inti  5

4.cp 拷贝文件

cp -r递归拷贝目录

cp-rf强制

5.mv的功能之一

剪切

重命名

6.将rm改造成mv

mkdir -p ~/.trash

7.cat/tac

正向，反向打印

8.more

分屏，逐行去显示

tac 文件名 | more

分屏，逐行倒着显示



 写数据 | 拿数据             |（管道）



head -50 file | tail -10

echo"hello"

echo"hello">hello.c

echo"hello">>hello.c

重定向    >

追加重定向    >





9.find命令会一个一个去找，find命令有可能访问硬盘，也有可能不访问硬盘.

10.   czf    xzf

11.ctrl +d 快速退出

top任务管理器

12.bash是具体的shell，类似于王婆

超级用户的命令提示符是#，普通用户的命令提示符是$

13.第一列是文件类型

d文件夹

-普通类型

l链接文件

b块设备文件

p管道文件

c字符设备

s套接口文件

linux下，可执行文件是以-开头。

所有者(拥有者)(u)，所属组(g)，other(o)   一一对应9个



chmod o+w a.out

chmod u-rwx,o-rwx a.out

chmod u+r,g+w,o+x a.out

chmod 777 a.out

chmod 000 a.out



改变拥有者用户

chown haote216 a.out

chown root a.out



改变所属组用户

chgrp haote216 a.out

chgrp root a.out



umask 去掉默认权限   0002

文件最终的权限=缺省的权限**去掉**umask中出现的权限



粘滞位只能给目录设置。

进入一个目录得有可执行的权限。

显示一个目录得有读的权限。

创建一个文件得有写的权限。





github:Couraerwang

yum install git

git clone

git add .

git commit -m "日志内容"     注：日志内容不能乱写，以后看信息时要用。

git push origin master 

然后登陆信息就行了

git log看所有日志信息



vim命令模式/插入模式/底航模式

命令模式   u删除当前行



