### 走近Linux

> 关机

在linux领域内大多用在服务器上,很少遇到关机的操作。毕竟服务器上跑- -个服务是永无止境的,除非特殊情况下,不得已才会关机。

```yaml
sync #将数据由内存同步到硬盘中。

shutdown #关机指令，你可以man shutdown 来看-一下帮助文档。例如你可以运行如下命令关机:

shutdown -h 10 #这个命令告诉大家，计算机将在10分钟后关机

shutdown -h now #立马关机

shutdown -h 20:25 #系统会在今天20:25关机

shutdown -h +10 #十分钟后关机

shutdown -r now #系统立马重启

shutdown -r +10 #系统十分钟后重启

reboot #就是重启，等同于shutdown -r now

halt #关闭系统，等同于shutdown -h now和poweroff

```

最后总结一下,不管是重启系统还是关闭系统,首先要运行sync命令,把内存中的数据写到磁盘中。



> 系统目录结构

1、一切皆文件

2、根目录/ ,所有的文件都挂载在这个节点下

登录系统后,在当前命令窗口下输入命令:

```yaml
ls /
```

* /bin : bin是Binary的缩写, 这个目录存放着最经常使用的命令。
* /boot :这里存放的是启动Linux时使用的- -些核心文件,包括一 些连接文件以及镜像文件。 (不要动)
* /dev : dev是Device(设备)的缩写，存放的是Linux的外部设备,在Linux中访问设备的方式和访问文件的方式是相同的。
* **/etc :这个目录用来存放所有的系统管理所需要的配置文件和子目录。**
* **/home :用户的主目录,在Linux中,每个用户都有一-个自己的目录, -般该目录名是以用户的账号命名的。**
* /lib:这个目录里存放着系统最基本的动态连接共享库,其作用类似于Windows里的DLL文件。 (不要动)
* /lost+found :这个目录一般情况下是空的,当系统非法关机后,这里就存放了一些文件。(存放突然关机的- -些文件)
* /media : linux系统会自动识别一-些设备,例如U盘、光驱等等,当识别后, linux会把识别的设备挂载到这个目录下。
* /mnt :系统提供该目录是为了让用户临时挂载别的文件系统的,我们可以将光驱挂载在/mnt/上,然后进入该目录就可以查看光驱里的内容了。( 我们后面会把- -些本地文件挂载在这个目录下)
* **/opt :这是给主机额外安装软件所摆放的目录。比如你安装一 个ORACLE数据库则就可以放到这个目录下。默认是空的。**
* /proc :这个目录是- - 个虚拟的目录,它是系统内存的映射,我们可以通过直接访问这个目录来获取系统信息。 (不用管)
* /root :该目录为系统管理员,也称作超级权限者的用户主目录。==
* /sbin : s就是Super User的意思,这里存放的是系统管理员使用的系统管理程序。
* /srv :该目录存放一-些服务启动之后需要提取的数据。
* /sys :这是linux2.6内核的-一个很大的变化。该目录下安装了2.6内核中新出现的一个文件系统sysfs。
* /tmp :这个目录是用来存放一些临时文件的。
* **/usr :这是一-个非常重要的目录,用户的很多应用程序和文件都放在这个目录下,类似于windows'下的program files目录。**
* /usr/bin :系统用户使用的应用程序。
* /usr/sbin :超级用户使用的比较高级的管理程序和系统守护程序。
* /usr/src :内核源代码默认的放置目录。
* **/var :这个目录中存放着在不断扩充着的东西,我们习惯将那些经常被修改的目录放在这个目录下。包括各种日志文件。**
* /run :是一个临时文件系统,存储系统启动以来的信息。当系统重启时,这个目录下的文件应该被删掉或清除。
* **/www :存放服务器网站相关的资源,环境,网站的项目**



### 常用的基本命令

clear ：清屏

> ls ：列出目录

-a参数：all，查看全部的文件,包括隐藏文件

-l参数：列出所有的文件,包含文件的属性和权限,没有隐藏文件

-al 参数：全部的文件,包括隐藏文件，包含文件的属性和权限,没有隐藏文件

-ll  参数：全部的文件,包括隐藏文件，包含文件的属性和权限,没有隐藏文件



> cd命令

cd ：切换目录命令

./ ：当前目录

cd.. ：返回上级目录



> pwd  显示当前用户所在的目录

[root@iZ2ze810avj5f8ryr5rqx4Z bin]# pwd
/bin



> mkdir ：创建目录

创建单个目录

[root@iZ2ze810avj5f8ryr5rqx4Z home]# mkdir test  

创建层级目录

[root@iZ2ze810avj5f8ryr5rqx4Z home]# mkdir -p test1/test2/test3



> rmdir：删除目录

删除单个目录

[root@iZ2ze810avj5f8ryr5rqx4Z home]# rmdir test  

删除层级目录

[root@iZ2ze810avj5f8ryr5rqx4Z home]# rmdir -p test1/test2/test3



> cp：复制文件或者目录

cp 复制的文件 复制到的地方 



> rm：移除文件或者目录

-f：忽略不存在的文件,不会出现警告,强制删除!

-r：递归删除目录!

-i：互动,删除询问是否删除

rm -rf /	#系统中所有的文件就被删除了，删库跑路就是这么操作的!



> mv  移动文件或者目录  也可以重命名文件

-f 强制

-u 只替换已经更新过的文件



### 基本属性

[root@iZ2ze810avj5f8ryr5rqx4Z /]# ls -ll
total 68
lrwxrwxrwx.  1 root root     7 Feb 20 10:57 bin -> usr/bin
dr-xr-xr-x.  5 root root  4096 Feb 20 11:10 boot
drwxr-xr-x  19 root root  2960 Apr  9 10:13 dev
drwxr-xr-x. 81 root root  4096 Apr  9 13:02 etc
drwxr-xr-x.  4 root root  4096 Apr  9 22:22 home
lrwxrwxrwx.  1 root root     7 Feb 20 10:57 lib -> usr/lib
lrwxrwxrwx.  1 root root     9 Feb 20 10:57 lib64 -> usr/lib64
drwx------.  2 root root 16384 Feb 20 10:57 lost+found
drwxr-xr-x.  2 root root  4096 Apr 11  2018 media
drwxr-xr-x.  2 root root  4096 Apr 11  2018 mnt
drwxr-xr-x.  2 root root  4096 Apr 11  2018 opt
drwxrwxrwx   2 root root  4096 Apr  9 12:53 patch
dr-xr-xr-x  90 root root     0 Apr  9 10:13 proc
dr-xr-x---.  6 root root  4096 Apr  9 12:59 root
drwxr-xr-x  24 root root   680 Apr  9 13:02 run
lrwxrwxrwx.  1 root root     8 Feb 20 10:57 sbin -> usr/sbin
drwxr-xr-x.  2 root root  4096 Apr 11  2018 srv
dr-xr-xr-x  13 root root     0 Apr  9 18:13 sys
drwxrwxrwt.  9 root root  4096 Apr  9 13:04 tmp
drwxr-xr-x. 14 root root  4096 Dec 13  2016 usr
drwxr-xr-x. 19 root root  4096 Apr  9 12:52 var
drwxr-xr-x   6 root root  4096 Apr  9 12:47 www

实例中, boot文件的第一个属性用"d"表示。 "d"在Linux中代表该文件 是一个目录文件。
在Linux中第一个字符代表这个文件是目录、 文件或链接文件等等:

* 当为[d ]则是目录
* 当为[- ]则是文件;
* 若是[1]则表示为链接文档( link file ) ;
* 若是[b ]则表示为装置文件里面的可供储存的接口设备(可随机存取装置) ;
* 若是[c]则表示为装置文件里面的串行端口设备,例如键盘、鼠标( - -次性读取装置)。

接下来的字符中，以三个为- -组,且均为「rwxJ的三个参数的组合。
其中，[r ]代表可读(read)、[ W ]代表可写(write)、[x ]代表可执行(execute)。
要注意的是,这三个权限的位置不会改变,如果没有权限,就会出现减号[ - ]。

三个参数的组合 ：root		管理组或游客 	其他用户

> chmod :更改文件9个属性(必须要掌握)

你没有权限操作此文件!

```bash
chmod [-R] xyz 文件或目录
```

Linux文件属性有两种设置方法,一种是数字I, -种是符号。
Linux文件的基本权限就有九个,分别是owner/group/others三种身份各有自己的read/write/execute权限。
先复习一下刚刚上面提到的数据:文件的权限字符为: [-rwxrwxrwxJ ， 这九个权限是三个三个一-组的!其中,我们可以使用数
字来代表各个权限,各权限的分数对照表如下:

```bash
r:4		w:2		x:1

可读可写不可执行	rw-		6
可读可写不课执行	rwx		7

chomd 777	文件赋予所有用户可读可执行!
```

每种身份(owner/group/others)各自的三个权限(r/w/x)分数是需要累加的,例如当权限为: [-rwxrwx---] 分数则是:

* owner=rwx=4+2+1=7
* group= rwx= 4+2+1 = 7
* others----= 0+0+0=0

```bash
chmod 777 filename
```





