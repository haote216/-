```
echo  重定向
echo “hello world” >> name     

输出重定向  >
追加重定向  >>
输入重定向  <
```

```
|     管道：连接俩条命令，将左侧命令的输出作为右侧命令的输入
ls /usr/bin/ -l | more 将内容逐行显示
```

```
more:只能回车往下翻
less:可以往上翻看 往下翻看
```

```
head:默认显示前10行
    head -12:打印前12行
tail：默认显示后10行
```

```
stat：查看文件的属性
```

##### date:

```
     指定格式显示时间：date +%Y:%m:%d-%H:%M:%S
    时间戳:Unix时间戳是从1970年1月1日开始所经过的秒数，不考虑闰秒。
    date +%s（后面用来定位错误的时间）
    date +%Y:%m:%d-%H:%M:%S -d@1529408239  把时间戳按正常时间显示出来 
```

```
cal:查看日历
     cal -3:显示系统前一个月，当前月，下一个月的月历
```

```
find:查找文件......
    find . -name file:在当前目录下查找名字为file的文件
```

```
grep:在文件中搜索字符串，将找到的行打印出来
    grep '999' file：在file文件中查找999这个字符串
    ps -aux | grep 'scsi'
    -i:忽略大小写
    -n:顺便输出行号
```

```
zip/unzip:打包/解包
    zip file.zip dir/*:把dir目录下的所有文件打包成file.zip
    unzip file.zip -d ./tmp:将包解压到./tmp路径下
```

```
tar:打包/解包
  c:创建
  x:解开
  z:压缩
  f:使用档名
```

```
bc:linux下的计算器
```

```
uname:获取电脑和操作系统的相关信息
  -a:依次为内核名称，主机名，内核版本号，内核版本，硬件名，处理器类型，硬件平台类型，操作系统名称
```

##### 热键：

```
TAB:命令补全和档案补齐
ctrl +c:让当前程序停掉
ctrl +d:键盘输入结束，也可以用来取代exit
关机:shutdown 
```

#### shell

```
shell:命令行解释器，是操作系统的外壳程序
作用:将使用者的命令翻译给核心（kernel）处理，同时，将核心的处理结果翻译给使用者。

linux下所使用的shell叫bash
windows下所使用的shell是windows界面
```

### **权限**

```
Linux下俩种用户：超级用户（root）、普通用户
普通用户：自己
超级（root）用户：可以做任何事情，几乎不受权限

退出root用户:ctrl+d    或     exit
```

##### Linux权限管理

```
文件和文件目录的所有者：u--User
文件和文件目录的所有者所在的组的用户：g--Group
其它用户：o--Others
```

![1551958125656](C:\Users\haote216\AppData\Roaming\Typora\typora-user-images\1551958125656.png)

  

```
文件类型
    d：文件夹
    -：普通文件
    l：软链接（类似于快捷方式）
    b：块设备文件（硬盘、光驱）
    p：管道文件
    c：字符设备文件（例如屏幕等串口设备）
    s：套接口文件    
```

###### chmod：设置文件的访问权限

```
1.chmod u-r file   去掉file文件的拥有者的读权限
  chmod g-w file   去掉file文件的所属组的写权限
  chmod o+x file   加上file文件的其他用户的可执行权限
  chmod u+rwx file 加上file文件的拥有者的所有权限
  chmod u-rwx,g+rwx,o+rwx file
2.r=4，w=2，x=1
chmod 777 file 将文件的所有权限改成可读可写可执行
chmod 666 file 将文件的所有权限改成可读可写

如果想看一个目录里的文件，需要可读r权限
如果想在一个目录创建文件，需要可写w权限
如果想在一个目录里进入文件，需要可执行x权限
```

###### chown：修改文件的拥有者

```
chown root file
chown haote216:haote216 file
```

###### chgrp:修改文件或目录的所属组

```
chgrp root file
```

##### umask:查看或修改文件掩码

```
新建文件夹默认权限=0666-权限掩码
新建目录默认权限=0777-权限掩码

普通用户默认为0002，超级用户默认掩码值为0022

注意：目录是：0666 rw-rw-rw-  减去0003  -------wx  得0664 rw-rw-r--
```

```
file：查看文件类型
```

##### 粘滞位

```
粘滞位通常是对目录文件进行设置，表示的含义是在被设置目录里面的文件只有拥有者或者更高权限才可以删除
1.创建文录
2.把当前目录改成root：root
3.把文件权限改成777
4.在当前目录touch一个文件root_file
5.用普通用户rm这个root_file  结果文件被删
6.重复之前，给目录加上粘滞位chmod +t 目录，结果文件无法删除
```

