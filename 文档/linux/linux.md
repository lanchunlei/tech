## Linux基础

### 概念

**Linux是一个操作系统，相比于windows，linux以它的【可靠】和【安全】著称，而且更新频繁**

**GNU项目**

* GNU is not unix;
* 一个类unix的操作系统；unix不是免费的；

**linux和GNU项目的联系**

* 这两个项目是互补的：linux其实就是写了一个类unix的内核；
* 1991年，GNU项目已经创建了不少操作系统的外围软件了；
* GNU的软件：cp命令，rm命令，Emacs，GCC，GDB 等；
* GNU项目+Linux(系统内核)=GNU/LINUX完整的操作系统

总结

* 操作系统的核心称为“内核”，但内核并不等于操作系统；
* 内核提供系统服务，比如文件管理、虚拟内存、设备I/O等；
* 单独的Linux内核没办法工作，须要有GNU项目的众多应用程序；



### linux发行版

* RedHat： 性能稳定，收费的是RHEL;
* Fedora: RedHat的社区免费后继版；
* CnetOS: RHEL的克隆版，免费；
* Debian: 迄今为止，最遵循GNU规范的Linux系统；
* Ubuntu: Debian的后继或一个分支；

### 安装centos

**virtual box**



### 文件系统

```
操作系统的文件数据除了实际内容之外，通常含有非常多的属性。文件权限与文件属性通常会分别存放在inode和block中。
```

**inode和block**

```
文件是存储在硬盘上的，硬盘的最小存储单位叫做扇区sector，每个扇区存储512字节。操作系统读取硬盘的时候，不会一个个扇区地读取，这样效率太低，而是一次性连续读取多个扇区，即一次性读取一个块block。这种由多个扇区组成的块，是文件存取的最小单位。块的大小，最常见的是4kb，即连续8个sector组成一个block。
文件数据存储在块中，那么还必须找到一个地方存储文件的元信息，比如文件的创建者、创建日期、大小等。这种存储文件元信息的区域就叫做 inode，中文译名为索引节点，也叫i节点。因此，一个文件必须占用一个inode，但至少占用一个block。
```

* 元信息 -> inode
* 数据 -> block

------

**inode内容**

```
inode包含很多的文件元信息，但不包含文件名，如字节数、属主UserID、属组GroupID、读写执行权限、时间戳等。
而文件名存放在目录当中，但Linux系统内部不使用文件名，而是使用inode号码识别文件。对于系统来说文件名只是inode号码便于识别的别称。
```

**inode号码**

```
表面上，用户通过文件名打开文件，实际上，系统内部将这个过程分为三步：
1. 系统找到这个文件名对应的inode号码；
2. 通过inode号码，获取inode信息；
3. 根据inode信息，找到文件数据所在的block，并读出数据。

其实系统还要根据inode信息，看用户是否具有访问的权限，有就指向对应的数据block，没有就返回权限拒绝。
```

ls -i

* 直接查看文件i节点号，也可以通过 stat 查看文件 inode 信息 i 节点号；

**inode大小**

```
inode也会消耗硬盘空间，所以格式化的时候，操作系统自动将硬盘分成两个区域。一个是数据区，另一个是inode区，存放inode所包含的信息。每个inode的大小，一般是128字节或256字节。通常情况下不需要关注单个inode的大小，而是需要重点关注【inode总数】。inode总数在格式化的时候就确定了。
```

df -i

* 查看硬盘分区的inode总和已使用情况；

**特有现象**

```
由于inode号码与文件名分离，导致一些unix/linux系统具备以下几种特有的现象
```

* 文件名包含特殊字符，可能无法正常删除。这时直接删除inode，能够直到删除文件的作用；
* 移动文件或重命名文件，只是改变文件名，不影响inode号码；
* 打开一个文件以后，系统就以inode号码来识别这个文件，不再考虑文件名；

```
这种情况使得软件更新变得简单，可以在不关闭软件的情况下进行更新，不需要重启。因为系统通过inode号码识别运行中的文件，不通过文件名，更新的时候，新版文件以同样的文件名，生成一个新的inode，不会影响到运行中的文件。等到下一次运行这个软件的时候，文件名就自动指向新版文件，旧版文件的inode则被回收。 ？？？
```

**inode耗尽故障**

```
由于硬盘分区的inode总数在格式化后就已经固定，而每个文件必须有一个inode，因此就有可能发生inode节点用光，但硬盘空间还剩不少，却无法创建新文件。同时这也是一种攻击的方式，所以一些公用的文件系统就要做磁盘限额，以防止影响到系统的正常运行。

至于修复，很简单，只要找出哪些大量占用i节点的文件删除就可以了。
```



### 基础命令

* 命令提示符：[root@bogon ~]# 

  root: 当前用户

  @ 前面是用户名，后面是所在的域

  bogon: 主机名 hostname

  ~ 当前用户家目录

  #($)：指示你所具有的权限，# 超级用户   $ 普通用户

  whoami 查看当前用户

  hostname 查看主机名

* 一些简单命令

  * date
  * ls
    * 列出当前目录下的文件和目录

* 命令的参数

  * 参数是写在命令之后的一些补充选项，命令和参数之间有空格隔开；

    command parameters

  * 参数里可以包含多个参数，由空格隔开；

  * 短参数（一个字母）

    * command -p

    * 一次加好几个短参数，用空格隔开

      command -p -a -T -c

    * 多个短参数可以合并在一起

      command -paTc

  * 字母的大小写有区别

  * 长参数(多个字母)

    * command --parameter

    * 组合使用短参数和长参数

      command -paTc --paramter1 --parameter2

  * 赋值
    * command -p 10
    * command --parameter=10
  
* 查找命令

  * tab 补全

    * 按一次提示
    * 按两次列出所有

  * 命令历史记录

    * 向上键

    * 向下键

    * ctrl + r   查找使用过的命令

    * history 列出之前使用过的所有命令

      ！+ 行号： 执行相应命令

* 常用快捷键

  * ctrl + l
  * ctrl + d:  给终端传递EOF(End Of File, 文件结束符)
  * ctrl + a: 光标跳到开头
  * ctrl + e：跳到末尾
  * ctrl + u: 删除所有在光标左侧的命令字符
  * ctrl + k：删除所有在光标右侧的命令字符
  * ctrl + w:  删除光标左侧的一个“单词”，用空格隔开的字符串
  * ctrl + y:   粘贴之前用ctrl+u, ctrl+k, ctrl+w等删除的字符串，类似 “剪切-粘贴”

* 文件组织，pwd和whic命令

  * linux的目录组织

    * 一切皆文件

    * 根目录  /

    * 目录结构

      /usr/bin

    * 直属子目录

      * bin

        二进制文件，可执行文件，包含了会被所有用户使用的可执行程序

      * boot

        linux启动密切相关的文件

      * dev

        device, 包含外设，它里面的子目录，每一个对应一个外设

      * etc

        包含系统的配置文件

        这下面放的都是一堆零零碎碎的东西，就叫etc好了，是历史遗留。

      * home

      * lib

        库

        包含被程序所调用的库文件，如.so结尾的文件，windows下 .dll

      * media

        可移动外设(usb, sd卡，dvd)插入电脑时，linux可以让我们通过media的子目录来访问这些外设中的内容

      * mnt

        挂载，类似media目录，一般用于临时挂载

      * opt

        optional application software package的缩写

        用于安装多数第三方软件和插件

      * root

      * sbin

        系统二进制文件，比bin目录多了一个前缀system

        包含系统级的重要可执行文件

      * srv

        service

        包含一些网络服务启动之后所需要取用的数据

      * tmp

        临时的

        普通用户和程序存放临时文件的地方

      * usr

        unix software resource的缩写

        表示“unix操作系统软件资源” (类似etc, 也是历史遗留的命名)

        usr是Linux最庞大目录之一，类似 windows中的 c:\Windows和c:\program files的集合

        usr目录里安装了大部分用户要调用的程序

      * var

        通常包含程序的数据，比如log文件

  * which

    which pwd ：pwd命令对应的可执行程序位于哪个(/usr/bin)目录中

* 目录相关命令

  * ls

    * list缩写，列出文件和目录 

      ls --color=auto    开启颜色

      蓝色  -  目录

      绿色  -  可执行文件

      红色  -  压缩文件

      浅蓝色 - 链接文件

      灰色  -  其他文件

    * ls -a

      显示所有文件和目录，包括隐藏的

      . 表示当前目录

      .. 表示上一级目录

    * ls -l

      详细信息列表，每一个文件或目录都有对应的一行信息

      drwxr-xr-x. 2 oscar oscar   6 jul 14 04:22 Destop

      文件权限： drwxr-xr-x 之类

      链接的数目： 2 ，1之类

      文件或目录所有者名称： oscar

      文件所在群组

      文件大小：6，   单位 byte

      最近一次修改时间

      目录或文件形式

    * -h  

      human readable

    * -t

      按修改时间倒序

  * cd

    change dirctory

  * du

    * disk usage缩写，磁盘占用

    * 显示目录包含的文件大小，相比ls -l，du命令统计的才是真正的文件大小

    * du -h

    * du -a 显示目录和文件的大小

    * du -s 只显示总计大小

      du -sh

* 文件操纵

  * cat 和 less

    * cat 一次性显示文件的所有内容

      -n 显示行号

      cat -n boot.log

    * less 分页显示文件内容

      空格键：下一页

      回车键：下一行

      b键：后退一页

      y键：后退一行

      q键：中止less命令

    * less高级功能

      * = 号 :  显示你在文件中的位置

        boot.log lines 1-23/155 byte 1296/8539 15%  (press RETURN)

        1-23 当前屏幕行号范围

        155 整个文件共155行

      * h键：显示帮助文档    q键：退出帮助文件

      * /    进入搜索模式

        n键：跳到下一个符合项目

        N键：跳到上一个符合项目

        正则表达式可以用在搜索内容中

  * head和tail，显示文件的开头和结尾

    * head 显示文件开头，默认显示10行

      head -n 20 boot.log 开头前20行

    * tail 显示文件结尾，默认显示10行

      * tail -n 5 boot.log 结尾5行

      * -f   实时追踪文件更新

        默认每1秒检查文件内容更新

        可以通过-s指定，tail -f -s 4 boot.log

  * touch

    * touch new_file_1 new_file_2 new_file_3

    * 创建一个空白文件

  * mkdir 

    * 创建一个目录
    * -p 递归创建目录结构

* 文件的复制、移动、删除和链接

  * cp

    * 拷贝文件或目录

    * cp new_file new_file_copy

    * cp new_file one/   复制后具有相同名称

    * cp new_file one/new_file_copy  复制后不具有相同名称

    * -r

      拷贝目录

      cp -r one one_copy

    * 通配符 *

      cp *.txt folder 把当前目录下所有的txt文件拷贝到 folder 目录中

  * mv

    * 移动文件或目录
    * 重命名文件或目录，不需要额外参数
    * 通配符 *

  * rm 

    * 终端中没有回收站，rm删除的文件一般比较难恢复
    * -i 确认是否删除
    * -f  强制删除
    * -r 递归删除目录及子文件
    * rm -rf  /

  * ln

    * 创建链接，相当于windows中快捷方式

    * 硬链接

      使文件1和文件2指向相同的inode，因此修改文件1或文件2，修改的是相同的一块内容

      只能创建指向文件的硬链接，不能创建指向目录的

      ln file1 file2  创建file1的一个硬链接file2

      ls -i  显示inode

      对于硬链接，删除任意一方的文件，共同指向的文件内容并不会从硬盘上被删除

    * 软链接

      * -s 

        ln -s file1 file2

        <img src="linux.assets/image-20210302072206309.png" alt="image-20210302072206309" style="zoom:33%;" />

      * file2这个软链接只是file1的一个快捷方式，它指向的是file1，所以显示的是file1的内容

      * 如果删除file1，file2变成死文件

      * 软链接可以指向目录，硬链接不行


### 用户和权限

**用户管理**

* sudo：暂时成为root

  sudo command

  sudo su：一直成为root

  su(centos) 切换root, 在个人用户家目录

  su -   切换root，且定位root家目录

* useradd

  * useradd username
  * passwd username

* userdel

  * userdel username   只删除用户，不删除用户的家目录
  * userdel -r username 连同家目录一并删除

**群组管理**

Linux中每一个用户都属于一个特定的群组，如果不主动设置，默认会创建一个与用户名一样的群组。

* groupadd

  * groupadd groupname

* usermod

  * usermod -g groupname username  将用户添加到一个群组，删除原群组
  * usermod -G g1,g2,g3 username  将用户添加到多个群组，删除原群组
  * uesrmod -aG groupname username 将用户添加到指定群组，不删除原群组

* groups

  * 当前用户所在群组

  * groups username  用户属于哪个群组

* groupdel groupname 删除群组

* chown

  * change owner   改变文件所有者，需要root运行

  * chown username filename   只改变文件所有者，不改变群组

  * chown username:groupname filename  改变文件所有者和群组

  * -R  递归改变目录的所有子目录和子文件

    chown -R username:groupname foldername

* chgrp

  * change group  改变文件所在群组
  * chgrp groupname filename

* chmod   修改访问权限

  <img src="linux.assets/image-20210303064602782.png" alt="image-20210303064602782" style="zoom:33%;" />

  * 如上
    * 属性：表示 文件 or 目录
    * 所有者：表示所有者对此文件的访问权限
    * 群组用户：表示文件所属群组用户对于此文件的访问权限
    * 其他用户：表示除前面用户外的其他用户对此文件的访问权限

  * linux系统里，文件和目录都有一列权限属性，指明谁有读的权利、写的权利、运行的权利；

  * 文件访问权限符

    d,  r,  w,  l,  x， .

    * d, directory，表示“目录”，  - 表示文件，或没有相应权限
    * r，read，读权限
    * w，write, 写权限
    * l，链接
    * x，execute，执行/运行，如果x在一个目录上，表示可以打开此目录看其子目录和子文件
    * .   点表示“SELinux安全标签”，有点表示启用SELinux安全标签

  * 解析  -rw-rw-r--

    * 第一个 - 表示普通文件，非目录
    * 第二个 rw- 表示文件的所有者对文件有读写权限，没有运行权限(普通文件，默认没有可执行权限)
    * 第二个 rw- 表示文件所在群组的用户对文件有读写权限，没有运行权限
    * 第三个 r-- 表示其他用户只有读权限，不能写，不能执行

  * chmod 不需要root用户才能执行，只要是文件所有者，就可以使用chmod修改文件的访问权限

    * chmod的绝对用法，用数字来分配权限
      * r  4,  w  2,  x  1
      * chmod 777 filename
      * 777 ，表示文件的所有者、群组用户、其他用户都有读、写和运行的权限
    * chmod的相对用法，用字母来分配权限
      * u: user 所有者， g: group 群组用户， o: other 其他用户，a:all 所有用户
      * +号 表示添加相应权限， -号 表示去除相应权限， =号 表示分配权限
      * chmod u+rx filename, 文件filename的所有者增加读和执行权限
      * -R 递归修改文件访问权限

### 软件安装

**软件包**

* 一个软件包(package)其实是软件的所有文件的压缩包
* 二进制形式的，包含了安装软件的所有指令
* 在red hat一族里，软件包的后缀是 .rpm  （Red Hat Package Manager）

**依赖关系**

* 一个软件经常需要使用其他程序或者其他程序的片段(称之为【库】)；
* 往往依赖关系还有下层依赖关系，环环相扣；

**软件仓库**

* linux的软件包都存放在一个地方，称之为软件仓库(repository)；

* 修改软件仓库

  /etc/yum.repos.d/CentOS-Base.repo

  https://www.centos.org/download/mirrors

**yum**

* centos中默认的包管理工具，Red Hat一族

* yum update/upgrade  更新软件包
  
  * 不加特定软件，更新所有软件
  
* yum search  搜索软件包

* yum install  安装软件包

  yum install emacs

  yum install -y  不再提示选择 y/n

* yum remove  删除软件包

  yum remove emacs

**rpm包**

* 有些软件包在软件仓库中没有，需要去官网下载 rpm包，使用rpm命令安装

* rpm -i *.rpm  安装软件包

  yum localinstall *.rpm

* rpm -e *.rpm  卸载软件包

  yum remove *.rpm

### Linux手册

**man**

* yum install -y man-pages   安装

* mandb  更新

* man command

  man ls

* q键退出手册

* 区域

  * name

  * synopsis  列出命令所有可能的方法

    * ls [OPTION] ... [FILE] ...

    * []  表示可选

    * ...  表示可以有多个选项或多个目录名

    * 粗体 表示要原封不动的输入

    * 下划线 表示用实际内容替换

    * a|b  a或b
  
  * description

**apropos**

* apropos sound  列出所有使用手册中有sound关键字的命令

**其他**

* -h或--help参数

  * 显示帮助文档
  * 没有man命令显示的详细，但是也很有用，易于阅读
  * yum -h

* whatis   列出man命令显示的手册的开头部分，就是概述命令的作用

  whatis ls

### 查找文件

* **locate**  filename
  
  * locate命令是搜索【包含】关键字的所有文件和目录
  * 文件数据库
    * locate命令不会对实际的整个硬盘进行查找，而是在文件数据库里查找记录
    * 文件数据库：包含文件列表和文件位置
    * linux系统一般每天更新一次文件数据库，所以刚创建的文件可能未被收录进文件数据库
  * updatedb 强制系统立即更新文件数据库，只能root执行
  
* **find**

  * 不在文件数据库中查找记录，遍历实际硬盘

  * find 何处 何物 做什么 

    * 何物：必须，要查找什么，可以根据文件名、大小、最近访问时间等进行查找

    * 何处：指定在哪个目录中查找，包括子目录，默认在当前目录及其子目录查找

    * 做什么：对找到的文件做一定的操作(后续处理)

    * find /home -name file_name

    * find / -size +10M     M k G   没有加减号表示等于

    * find / -name "*.txt" -atime -7, 最近7天访问过的

    * 操作查找结果

      * 默认find命令只列出查找到的文件

      * 可以将其用格式化的方式打印出来

      * 使用 -printf 参数

        find / -name "*.txt" -printf "%p - %u\n"

      * -delete

      * -exec

        find /home/one -name "*.txt" -exec chmod 600 {} \;  

        ｛｝用查找到的文件替代

           \;   **必须的结尾，包括分号**



## 进阶知识

### 数据处理

* grep  筛选数据

  * grep path /etc/profile   列出这个文件中包含path的行，如果path中包含空格需要使用 “”

  * **-i  忽略大小写，默认区分大小写**

    grep -i path /etc/profile

  * **-n  显示行号**

    grep -in path /etc/profile

  * **-v   显示文本不在的行**

    grep -v path /etc/profile

  * -r   递归查找

    ```
    grep -r abcdefg test/
    test/a/b/c/abc.txt:abcdefghi
    ```

  * 配合正则表达式

    <img src="linux.assets/image-20210304070838494.png" alt="image-20210304070838494" style="zoom: 25%;" />

    -E  使用正则表达式，可以不加

    grep -E ^Path /etc/profile  匹配行首

    grep -E [a-zA-Z]  /etc/profile  包含在a至z，A至Z之间的任意字母的行

* sort

  * sort name.txt   按字母顺序排序
  * -o
    * 将排序后的内容写入新文件
    * sort -o name_sorted.txt name.txt
  * **-r  倒序**
  * -R  随机排序
  * **-n  对数字排序**

* wc 

  * **word cound，文件的统计，可以统计行数、u单词数、字节数等**
  * wc name.txt
  * -l  只统计行
  * -w  只统计单词
  * -c  统计字节数

* uniq  

  * 删除文件中的重复内容，只能将连续的重复行变为一行

* cut

  * -c  根据字符数剪切

    cut -c 2-4 name.txt

  * -d 按指定符号分隔

    cut -d , -f 1 notes.csv  # -f指定需要提取的字段编号 （以逗号分隔的第一个区域）

* 流

* **管道**

  <img src="linux.assets/image-20210305065241283.png" alt="image-20210305065241283" style="zoom:25%;" />

  * 把两个命令连起来使用，一个命令的输出作为另一个命令的输入，pipeline

  * ｜ 管道符号，建立命令管道

  * 实践

    ```
    cut -d, -f 1 notes.csv | sort
    
    du | sort -nr | head
    ```

* **重定向**

  <img src="linux.assets/image-20210305061551935.png" alt="image-20210305061551935" style="zoom: 25%;" />

  * 把本来要显示在终端的命令结果，输送到文件中或作为其他命令的输入(命令的链接或叫命令的管道)

  * ">"和">>"  重定向到文件

    ```
    cut -d, -f 1 notes.csv > students.txt
    ```

    * ">" 如果此文件不存在则新建，如果存在则覆盖
    * ">>" 重定向到文件末尾，追加，不会覆盖

  * **stdin, stdout, stderr**

    <img src="linux.assets/image-20210305063431382.png" alt="image-20210305063431382" style="zoom:25%;" />

    * stdin: 标准输入流，指输入至程序的数据(通常是文件)

    * stdout: 标准输出，指终端输出的信息，不包含错误信息

    * stderr: 指终端输出的错误信息

      <img src="linux.assets/image-20210305063632339.png" alt="image-20210305063632339" style="zoom:25%;" />

    * 2>
      * 标准错误输出
      * cat noe_exist_file.txt 2> err.log
      
    * 2>>
      
      * 将标准错误输出重定向到(追加)文件末尾
      
    * 2>&1
      
      * 将标准输入和标准错误输出到一个文件
      
    * cat not_exist_file.txt > results.txt 2>&1
    
    * /dev/null 
    
      ```
      /dev/null 是一个特殊的设备文件，它丢弃一切写入其中的数据，可以将它视为黑洞，它第效于只写文件，写入其中的所有内容都会消失，尝试从中读取或输出不会有任何结果
      ```
    
      * /dev/null 通常被用于丢弃不需要的输出流，或作为用于输入流的空文件，这些操作通常由重定向完成，任何你想丢弃的数据都可以写入其中
  
* <
  
    * 从文件中读取
  * cat < notes.csv   与 cat notes.csv 运行结果一致
  
* <<
  
    * 从键盘读取
  * sort -n << END
  

### 进程和系统监测

* w

  * 都有谁，在做什么？

    * 快速了解系统中目前有哪些用户登录着，以及他们在干什么

      ```
      []$ w
      07:26:44 up 24 min, 2 users, load average: 0.77, 0.01, 0.09
      USER   TTY   FROM      LOGIN@   IDLE   JCPU    PCPU   WHAT
      oscar  :0     :0       07:02   ?xdm?   1:01    0.20s   /usr/libexec/g
      oscar  pts/0  :0       07:17    0.00s  0.14s   0.08s   w
      ```

    * load average: 0.77, 0.01, 0.09

      * 1分钟之内的平均负载

      * 5分钟之内的平均负载

      * 15分钟之内的平均负载

      * 负载指运行的进程数，单核处理器负载超过1，双核处理器负载超过2则过高

        ```
        单核处理器：负荷1.0说明系统已经没有剩余资源了，有时生产环境中将这条线划在 0.70
        ```

        

    * USER：用户名(登录名)

    * TTY:  登录的终端名称

      * :0 指本地，就是目前所在的这个图形终端
      * pts表示伪终端从属

    * FROM:  用户连接到的服务器的IP地址(或主机名)

    * LOGIN@:  用户连接系统的时间

    * IDLE:  用户有多久没活跃了(没运行任何命令)

    * **JCPU:  该终端所有相关的进程使用的CPU的时间**，当进程结束停止计时，开始新的进程则重新计时

      进程是加载到内存中运行的程序，一个程序可能产生多个进程

    * **PCPU: 表示CPU执行当前程序所消耗的时间**，当前进程是在what列显示的程序

    * WHAT：当下用户正运行的程序

  * who:  当前哪些用户登录

* ps和top查看进程

  * ps 默认列出当前用户进程

    ```
    ps
    
    PID TTY TIME CMD
    ```

    * 进程的静态列表

    * PID 进程号

    * TTY 哪个终端

    * TIME 运行了多久

    * CMD 哪个程序

    * **ps -ef**

      * 列出所有用户在所有终端的所有进程

        ```
        ps -ef
        
        UID    PID   PPID    C            STIME            TTY    TIME     CMD
        用户id 进程号 父进程号 进程占用cpu时间 进程启动到现在的时间
        
        ```

      TTY:该进程在哪个终端上运行，若与终端无关，则显示 ？
        TIME：使用掉的CPU的时间
        ```
        
        
        ```

    * ps -efH 树状列出

    * ps -uroot： 列出此用户运行的进程 

    * ps -aux:  通过CPU和内存使用来过滤进程

      ```
      uSER        PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
      root          1  0.0  0.1 128024  3196 ?        Ss   05:58   0:01 /usr/lib/systemd/systemd --switched-root --system --de
      root          2  0.0  0.0      0     0 ?        S    05:58   0:00 [kthreadd]
      root          4  0.0  0.0      0     0 ?        S<   05:58   0:00 [kworker/0:0H]
      root          5  0.0  0.0      0     0 ?        S    05:58   0:00 [kworker/u256:0]
      root          6  0.0  0.0      0     0 ?        S    05:58   0:00 [ksoftirqd/0]
      root          7  0.0  0.0      0     0 ?        S    05:58   0:00 [migration/0]
      root          8  0.0  0.0      0     0 ?        S    05:58   0:00 [rcu_bh]
      ```

      * 说明

        * %cpu: 进程占用的cpu百分比

        * %MEM：占用内存的百分比

        * VSZ:  该进程使用的虚拟内存量(KB)

        * RSS:  该进程占用的固定内存量(KB)

        * STAT：进程的状态

          ```
          D      //无法中断的休眠状态（通常 IO 的进程）；  
          R      //正在运行可中在队列中可过行的；  
          S      //处于休眠状态；  
          T      //停止或被追踪；  
          W      //进入内存交换 （从内核2.6开始无效）；  
          X      //死掉的进程 （基本很少见）；  
          Z      //僵尸进程；  
          <      //优先级高的进程  
          N      //优先级较低的进程  
          L      //有些页被锁进内存；  
          s      //进程的领导者（在它之下有子进程）；  
          l      //多线程，克隆线程（使用 CLONE_THREAD, 类似 NPTL pthreads）；  
          +      //位于后台的进程组
          ```

          

        * START：该进程被触发启动的时间

        * TIME：该进程实际使用CPU的时间

      * ps -aux | less
      * ps -aux --sort -pcpu | less  根据cpu使用率降序排列
      * ps -aux --sort -pmem | less   根据内存使用率降序排列
      * **ps -aux --sort -pcpu, +pmem | head**  将cpu和内存参数合并到一起，并通过管道显示前10个结果

    * pstree 以树形结构显示进程 

  * top

    * 进程的动态列表

    * 详细信息

    ```
    top - 09:23:22 up 1 min,  1 user,  load average: 2.26, 0.81, 0.29
    Tasks: 167 total,   1 running, 166 sleeping,   0 stopped,   0 zombie
    %Cpu(s):  5.9 us, 11.8 sy,  0.0 ni, 82.4 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
    KiB Mem :  1863012 total,    79384 free,  1593060 used,   190568 buff/cache
    KiB Swap:  2097148 total,  2091508 free,     5640 used.    77804 avail Mem 
    
       PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND                         
      1952 root      20   0  162124   2232   1528 R  6.2  0.1   0:00.02 top                             
         1 root      20   0  128028   3704   1220 S  0.0  0.2   0:01.13 systemd                         
         2 root      20   0       0      0      0 S  0.0  0.0   0:00.00 kthreadd                        
         3 root      20   0       0      0      0 S  0.0  0.0   0:00.00 kworker/0:0                     
         4 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 kworker/0:0H                    
         5 root      20   0       0      0      0 S  0.0  0.0   0:00.02 kworker/u256:0                  
         6 root      20   0       0      0      0 S  0.0  0.0   0:00.19 ksoftirqd/0                     
         7 root      rt   0       0      0      0 S  0.0  0.0   0:00.00 migration/0                     
         8 root      20   0       0      0      0 S  0.0  0.0   0:00.00 rcu_bh                          
         9 root      20   0       0      0      0 S  0.0  0.0   0:00.31 rcu_sched                       
    ```

    * 说明

      * top: 相当于动态的w命令
      
      * Tasks: 运行的任务：total, running, sleeping, stopped,  zombie（僵死）
      
      * %Cpu:
        * us: user cpu time，用户态使用的cpu时间比
        * sy: system cpu time, 系统态使用的cpu时间比
        * ni: user nice cpu time, 用做nice加权的进程分配的用户态cpu时间比
        
        * id: idle cpu time: 空闲的cpu时间比。如果该值持续为0，同时sy是us的两倍，则通常说明系统面临着cpu资源的短缺
        
        * wa: io wait cpu time: cpu等待磁盘写入完成时间。该值较高时，说明IO等待比较严重，这可能是磁盘大量随机访问造成的，也可能是磁盘性能出现了瓶颈
        
        * hi: hardware irq: 硬中断消耗时间
        
        * si: software irq: 软中断消耗时间
        * st: steal time: 虚拟机偷取时间
        * 以上这些参数的值加起来为 100%
        
      * KiB Mem：
      
        * buff/cache 缓存的内存量
      
      * KiB Swap: 交换分区
      
        ```
        swap,虚拟内存，当物理内存不足时，拿出部分硬盘空间当SWAP分区(虚拟成内存)使用，从而解决内存容量不足的情况。
        当某进程向OS请求内存发现不足时，OS会把内存中暂时不用的数据交换出去，放在SWAP分区中，这个过程称为SWAP OUT。
        当某进程又需要这些数据且OS发现还有空闲物理内存时，又会把SWAP分区中的数据交换回物理内存中，这个过程称为SWAP IN。
        SWAP大小是有上限的，一旦SWAP使用完，OS会触发OOM-Killer机制，把消耗内存最多的进程kill掉以释放内存。
        
        数据库系统嫌弃swap?
        1. 数据库系统对响应延迟比较敏感，使用swap势必影响性能，延迟太大和服务不可用没有任何区别，更严重的是，swap场景下进程就不不死，意味着系统一直不可用。。。如果不使用swap直接oom，这样很多高可用的系统直接会主从切换，用户基本无感知。
        2. 另外对于诸如Hbase这类分布式系统来说，并不担心某个节点宕掉，恰恰担心某个节点夯住。一个节点宕掉，最多就是小部分请求短暂不可用，重试即可恢复，但是一个节点夯住会将所有分布式请求都夯住，服务器线程资源被占用不放，导致整个集群请求阻塞，甚至集群被拖垮。
        
        swap工作机制
        Linux会在两种场景下触发内存回收：
        1.内存分配时发现没有足够空闲内存时会立刻触发内存回收；
        2.开启一个守护进程周期性对系统内存进行检查，在可用内存降低到特定阈值后主动触发内存回收。
        ```
      
        
      
      * 进程列表，默认按照使用处理器的比率排列
      
        * PR:  进程优先级
      
        * NI:  nice值，负值表示高优先级，正值表示低优先级
      
        * VIRT：进程使用的虚拟内存总量，单位kb。VIRT=SWAP+RES
      
          ```
          virtual memory usage, 虚拟内存
          1. 进程“需要的”虚拟内存大小，包括进程使用的库、代码、数据等
          2. 假如进程申请100m的内存，但实际只使用了10m，那么它会增长100m，而不是实际的使用量
          ```
      
        * RES：进程使用的、未被换出的物理内存大小，单位kb，RES=CODE+DATA
      
          ```
          resident memory usage 常驻内存
          1. 进程当前使用的内存大小、但不包括swap out
          2. 包含其他进程的共享
          3. 如果申请100m的内存，实际使用10m，它只增长10m，与VIRT相反
          4. 关于库占用内存的情况，它只统计加载的库文件所占内存大小
          
          DATA
          1. 数据占用的内存，如果top没有显示，按f键可以显示出来
          2. 真正的该程序要求的数据空间，是真正在运行中要使用的
          ```
      
        * SHR：共享内存大小，单位kb
      
        * S：进程状态。D:不可中断的睡眠状态  R:运行  S:睡眠  T:跟踪/停止  Z:僵尸进程
      
        * %CPU:  上次更新到现在的CPU时间占用百分比
      
        * %MEM: 进程使用的物理内存百分比
      
        * TIME+:  进程使用的CPU时间总计，单位 1/100 秒
      
      * ```
        top 运行中可以通过 top 的内部命令对进程的显示方式进行控制。内部命令如下：
        s – 改变画面更新频率
        l – 关闭或开启第一部分第一行 top 信息的表示
        t – 关闭或开启第一部分第二行 Tasks 和第三行 Cpus 信息的表示
        m – 关闭或开启第一部分第四行 Mem 和 第五行 Swap 信息的表示
        N – 以 PID 的大小的顺序排列表示进程列表
        P – 以 CPU 占用率大小的顺序排列进程列表
        M – 以内存占用率大小的顺序排列进程列表
        h – 显示帮助
        n – 设置在进程列表所显示进程的数量
        q – 退出 top
        s – 改变画面更新周期
        ```
      
        ```
        top使用方法
        
        使用格式：
        top [-] [d] [p] [q] [c] [C] [S] [s] [n]
        
        参数说明：
        
        d：指定每两次屏幕信息刷新之间的时间间隔。当然用户可以使用s交互命令来改变之。
        
        p:通过指定监控进程ID来仅仅监控某个进程的状态。
        
        q:该选项将使top没有任何延迟的进行刷新。如果调用程序有超级用户权限，那么top将以尽可能高的优先级运行。
        
        S：指定累计模式。
        
        s：使top命令在安全模式中运行。这将去除交互命令所带来的潜在危险。
        
        i：使top不显示任何闲置或者僵死进程。
        
        c:显示整个命令行而不只是显示命令名。
        ```
      
      * 常用命令说明
        * k: 终止一个进程，系统将提示用户输入需要终止的进程PID
        * i: 忽略闲置和僵死进程，这是一个开头式命令
        * q: 退出程序
        * s: 改变两次刷新之间的延迟时间
        * f或F: 从当前显示中添加或者删除项目，使用d键
        *  
      * 
    
  * 键盘按键
    
    * q 退出
      * h 帮助文档
      * u 按照用户过滤显示
      
    * glances
    
      更优秀的监控软件
    
  * vmstat

    ```
    procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
     r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
     2  0 387584  74816      0 411812    3   52  1148   155  240  395  6  3 92  0  0
    ```

    * procs
      * r列表示运行和等待cpu时间片的进程数，如果长期大于1，说明cpu不足，需要增加cpu时间
      * b列表示在等待资源的进程数，比如正在等待I/O、或者内存交换等
    * cpu
      * us: 列显示了用户方式下所花费 CPU 时间的百分比。us的值比较高时，说明用户进程消耗的cpu时间多，但是如果长期大于50%，需要考虑优化用户的程序
      * sy: 列显示了内核进程所花费的cpu时间的百分比。这里us + sy的参考值为80%，如果us+sy 大于 80%说明可能存在CPU不足
      * wa: 列显示了IO等待所占用的CPU时间的百分比。这里wa的参考值为30%，如果wa超过30%，说明IO等待严重，这可能是磁盘大量随机访问造成的，也可能磁盘或者磁盘访问控制器的带宽瓶颈造成的(主要是块操作)
      * id: 列显示了cpu处在空闲状态的时间百分比
    * system
      * in: 列表示在某一时间间隔中观测到的每秒设备中断数
      * cs: 列表示每秒产生的上下文切换次数，如当 cs 比磁盘 I``/O` `和网络信息包速率高得多，都应进行进一步调查
    * memory
      * swpd: 切换到内存交换区的内存数量(k表示)。如果swpd的值不为0，或者比较大，比如超过了100m，只要si、so的值长期为0，系统性能还是正常
      * free: 当前的空闲页面列表中内存数量(k表示)
      * buff: 作为buffer cache的内存数量，一般对块设备的读写才需要缓冲
    * 

    

* 停止进程

  * ctrl + c: 停止终端中正在运行的进程
  * kill pid
    * kill 8461 1706 8561
    * kill -9 pid   强制结束
    * killall nginx

* 关闭系统

  * halt
  * reboot
  * shutdown
  * poweroff

* 进程

  <img src="linux.assets/image-20210307103339402.png" alt="image-20210307103339402" style="zoom: 33%;" />

  * **&   后台运行**

    * cp file.txt file-copy.txt &
    * find / -name *.txt &
    * find / -name *.txt > find_output.txt &
    * find / -name *.txt > find_output.txt 2>&1

  * **nohup**

    * &符号虽然常用，但是其后台进程与终端关联，一旦终端关闭或者用户登出，进程就自动结束
    * nohup java -jar bkm.jar > bkm.log 2>&1

  * ctrl＋ｚ 转到后台，并暂停运行

  * bg 使进程转到后台

    * 假如命令已经在后台，并且暂停着，bg命令会将其状态改为运行
    * 不加参数，默认作用于最近的一个后台进程
    * bg %2 作用于编号为2的后台进程 (进程转入后台之后，会显示它在当前终端下的后台进程编号)

  * **5种常见的进程状态**

    * **运行**(正在运行或运行队列中等待)

      状态码**R**, runable（on run queue）

    * **中断**(休眠中，受阻)

      当某个条件形成后或接受到信号时，则脱离该状态

      状态码 **S**

    * **不可中断**

      进程不响应系统异步信息，即使用kill命令也不能使其中断

      状态码**D**，uninterruptible sleep

    * **僵死**

      进程已终止，但进程描述符依然存在，直到父进程调用wait4()系统函数后将进程释放

      状态码**Z**，a defunct(zombie) process

    * **停止**

      进程收到 SIGSTOP SIGSTP SIGTIN SIGTOU等停止信号停止运行

      状态码**T**，traced or stopped

  * jobs

    * 显示当前终端里的后台进程状态

  * fg

    * 使进程转为前台运行

* 定时和延时

  * date

    * 显示当前时间
    * date 10121430  修改当前系统时间

  * at

    * 延时执行一个程序，只能让程序执行一次

    * ```
      at 22:10 12/10/19  # 月/日/年
      at> touch file.txt
      at> <EOT>          # ctrl+d
      job 1 at Sun Sep 1 22:10:00 2019
      ```

    * 在指定间隔之后执行程序

      ```
      at now +10minutes
      ```

    * atq 列出正等待执行的at任务

    * atm 删除正在等待执行的at任务

  * && 和 || 符号 和 ;

    * &&: && 号前的命令执行成功，才会执行后面的命令
    * ||:  ||号前的命令执行失败，才会执行后面的命令
    * ;:  不论分号前的命令执行是否成功，都执行分号后的命令

  * crontab

    * 定时执行程序，at命令只能执行命令一次，crontab可以重复执行命令

    * crontab命令用来读取和修改crontab文件

    * crontab文件包含了你要定时执行的程序列表和执行时刻

    * crontab 和 cron

      * crontab 用于修改crontab文件
      * cron 用于实际执行定时的程序

    * 参数

      * -l:  显示crontab文件

      * -e：修改crontab文件

        ```
        编辑的格式：m h dom mon dow command
        m: minutes
        h: hour
        dom: day of month,表示“一个月的哪一天”
        mon: month
        dow: day of week, 星期几
        command: 需要定时执行的命令
        
        * 表示此位置空
        
        例：
        10 22 * * * touch ~/file.txt # 每天22:10在家目录下创建file.txt文件，路径最好用绝对路径
        ```

        <img src="linux.assets/image-20210308063118704.png" alt="image-20210308063118704" style="zoom: 50%;" />

      * -r:  删除crontab文件

* 文件压缩和解压

  * tar 

    * 将多个文件归档

    * 打包：将多个文件变成一个总的文件，这个总的文件通常称为archive(存档、归档)

    * 压缩：将一个大文件通过某些压缩算法变成一个小文件

    * 首先，用tar命令将多个文件归档为一个总的文件(archive)；然后用gzip命令将archive压缩为更小的文件

      <img src="linux.assets/image-20210308064500097.png" alt="image-20210308064500097" style="zoom:33%;" />

    * 用法

      * -cvf 创建一个tar归档

        tar -cvf sorting.tar sorting/

        tar -cvf archive.tar file1.txt file2.txt file3.txt

        c: create

        v：verbose, 表示冗余，会显示操作的细节

        f:  file，表示文件，指归档文件

      * -tf 显示归档里面的内容，并不解压

        ```
        tar -tf sorting.tar
        sorting/
        sortinig/QuickSort.java
        sorting/MergeSort.java
        ```

      * -rvf 追加文件到归档

        tar -rvf archive.tar file_extra.txt

      * -xvf  解开归档

        -cvf 的反操作

        x: extract，提取、取出

        tar -xvf sorting.tar

  * gzip(常用) 和 bzip2（压缩效率高，但更耗时）

    * .tar.gz: 用gzip命令压缩后的文件后缀名
    * gzip sorting.tar 压缩
    * gunzip sorting.tar.gz  解压

  * **tar 直接压缩并归档**

    * **tar -zcvf sorting.tar.gz sorting**
    * **tar -zxvf sorting.tar.gz**

  * zcat zmore zless

    * zcat sorting.tar.gz

  * zip unzip  

    * zip -r sorting.zip sorting   # -r 表示递归
    * unzip sorting.zip

* 编译安装软件

  **下载源代码 -> 解压压缩包 -> 配置 -> 编译 -> 安装**

  * 找安装包 -> 官网 -> .rpm（redhat package manager）

  * 编译安装

    * 编译就是将程序的源代码转换成可执行文件的过程

    * 实践 htop安装

      * 官网下载 htop.tar.gz

      * tar -zxvf htop.tar.gz  # 解压

      * cd      ./configure

        error

        yum install -y ncruses-devel

      * make && make install



### 安装centos服务器

* 查看IP
  * ifconfig 比较旧的命令，net-tools中的命令
  * ip addr 比较新的命令，iproute2中的命令
    * lo
    * enp0s3

* 网络

  <img src="linux.assets/image-20210310080547248.png" alt="image-20210310080547248" style="zoom: 50%;" />

  * 桥接模式

    ```
    将虚拟机的虚拟网络适配器与主机的物理网络适配器进行交接，虚拟机以此可访问外部网络；
    相当于在局域网中添加了一台新的、独立的计算机，因此，虚拟机会占用局域网中的一个ip地址，可以和其它终端进行相互访问；
    ```

  * NAT模式

    ```
    Network Address Translation，网络地址转换。
    vmware在主机上建立单独的专用网络，用以在主机和虚拟机之间相互通信，虚拟机向外部发送的请求数据“包裹”，都会交由NAT网络适配器加上“特殊标记”并以主机的名义转发出去，外部网络返回的响应数据“包裹”，也是先由主机接收，然后交由NAT网络适配器根据"特殊标记"识别并转发给对应的虚拟机，因此，虚拟机在外部网络中不必具有自己的IP地址。从外部来看，虚拟机和主机共享一个ip地址，默认情况下，外部网络终端也无法访问到虚拟机。
    通过手动修改NAT设置实现端口转发功能，将外部网络发送到主机指定端口的数据转发到指定的虚拟机上。
    ```

  * 仅主机模式

    ```
    比NAT模式更封闭的网络连接模式，它将创建完全包含在主机中的专用网络。
    默认情况下，使用仅主机模式网络连接的虚拟机无法连接到internet
    ```

  * 自定义网络连接模式

* SSH
  * Secure SHell
  * 加密
    * 对称加密
      * Symmetric-Key Encryption 对称密钥加密
      * 加密和解密使用同一个密钥，加密方和解密方都需要知道这个密钥
      * 致命缺陷：必须谨慎地传递密钥
    * 非对称加密
      * Asymmetric-Key Encryption,非对称密钥加密
      * 加密用于对称加密的密钥，称为非对称加密
      * 一个密钥(**公钥**)加密，一个密钥(**私钥**)解密
      * 消耗电脑资源，相比于对称加密慢 100 ~ 1000倍
      * RSA算法
    * SSH创建安全通信管理
      * SSH结合使用非对称加密和对称加密两种方法
      * 首先，使用**非对称加密**，安全地传输对称加密的密钥
      * 之后，一直使用**对称加密**的密钥来作为加密和解密的手段(效率高)
  * openssh

### vim

* Vi iMproved, vi文件编辑器的进阶版

* yum install vim

* vim的多种模式与操作

  * Interactive Mode，交互模式，默认，也称正常模式。每次运行vim程序的时候，就会进入这个模式
    * 不能输入文本
    * 可以在文本间移动、删除一行文本、复制粘贴文本
    * 跳到指定行、撤销操作等
  * Insert Mode，插入模式
    * 按 i 键进入插入模式，o 在当前行新增下一行进行插入
    * 按 s 删除光标所在处字符并进入插入模式，按S删除光标所在行，并进入插入模式
    * 按 esc 键退出插入模式
  * Command Mode，命令模式
    * 运行一些命令：保存、退出
    * 激活一些vim配置：语法高亮、显示行号等
    * 发送一些命令给终端命令行：ls  cp等
    * **在交互模式下按冒号键**

  * 可视模式
    * 相当于高亮选取文本后的 交互/模式
    * v  字符可视模式
    * V  行可视模式
    * ctrl+v  块可视模式
    * 在可视模式下 d 键 可实现删除选中字符
    * i 实现插入

* 交互模式

  * **按0(Home)回到行首，按$(End)回到行尾**
  * w 一个单词一个单词的移动
  * **x 删除字符，相当于 backspace**
    * 4x 删除4个字符
  * **d 删除单词或行**
    * **被删除的内容被暂存在内存里，就好像“剪切”**
    * dd 删除光标所在行
    * **2dd 删除从光标所在行开始的2行**
    * dw，删除一个单词，如果光标处于单词中间则只会删除从当前光标开始到下一个单词
    * 3dw 一次删除3个单词
    * **d0，按下d键，再加0键，就会删除从光标处到行首的所有字符**
    * **d$，按下d键，再加$键，就会删除从渔村处到行末的所有字符**
  * yy 复制行到内存中
    * vim中，y是复制的意思
  * p 粘贴
    * 如果之前用dd剪切过一行，或者用yy复制过一行，或者是同类的操作：y$, dw, y0, d0, d$等等，可以**使用p键来粘贴这些内容**
    * 用p来粘贴时，内容会被粘贴到光标之后
    * 如果用yy复制了一行，再用p粘贴，那么这一行会被粘贴到光标所在行的下一行处
    * **可以将同样的内容粘贴多次，只需要在p前面加上次数，如：7p**
  * r替换一个字符
    * 在交互模式下，将光标置于想要替换的字符上，按下r键，接着输入要替换的字符，如rs, 表示替换当前字符为s
    * R，切换到替换模式，左下角会显示 -- REPLACE --，此模式下可以一次性替换多个字符，esc退出
  * u 撤销操作
    * 撤销修改，4u表示撤销最近的四次修改
  * ctrl+r：重做之前的修改，取消撤销
  * g 跳转到指定行
    * vim中，每一行都有一个行号，行号从1开始
    * 命令模式下：set nu 显示行号
    * 7gg或7G, 跳转到第7行
    * G 跳到最后一行
    * gg 跳转到第一行
  * /   查找
    * 从当前光标处开始向文件尾搜索
    * /see  高亮显示查找到的字符，光标在第一个查找到的字符处，如果没找到，显示 pattern not found
    * n 查找下一个匹配项，N 查找上一个匹配项
  * ? 
    * 从当前光标处向文件开头进行搜索
  * :s 查找并替换
    * 命令模式下：   :s/旧字符串/新字符串
    * 只会替换光标所在行的第一个匹配的字符串为新字符串
    * :s/旧字符串/新字符串/g   替换光标所在行的所有匹配的旧字符串为新字符串
    * :数字,数字 s/旧字符串/新字符串/g    替换文件中第几行到第几行的所有匹配的旧字符串为新字符串
    * **:%s/旧字符串/新字符串/g   替换文件中所有匹配的字符串**
  * :r 合并文件
    * :r 实现在光标处插入一个文件的内容，如 :r 另一个文件名，可以用tab实例另一个文件的路径
  * 分屏
    * :sp 另一个文件名          横向分屏
    * :vsp 另一个文件名        垂直分屏
    * ctrl+w  从一个viewport移到另一个viewport
    * :quit或:close 退出分屏
  * :!command  运行终端命令，不离开vim的情况下运行外部命令
  
* 命令模式

  * :w 保存
  * :wq 保存并退出，或  :x
  * :q 退出
  * :q! 强制退出

* vim配置

  * 激活选项参数
    * 如果希望所做的配置是永久的，需要在家目录下创建一个vim的配置文件  **vimrc**
      * set nu
      * set mouse=a
      * set showcmd
      * set ignorecase
      * ...
  * 安装插件

## 网络和安全

* wget 
  * 从终端控制台下载文件，只需要给出文件的HTTP或FTP地址
  * 格式：wget [参数] [url]   # wget 可以显示下载进度
  * yum install wget
  * wget -c xxx   # -c 继续一个中断的下载

* scp

  * 网间安全拷贝

  * scp file.txt root@192.168.1.5:/root    # 从本地拷贝到另一台电脑(192.168.1.5, root用户下的root目录)

  * scp root@192.168.1.5:/root/file.txt file_changed_name.txt   # 从远程电脑(192.168.1.5)的用户root的/root目录把file.txt拷贝到我的电脑当前文件夹下，并改名

  * scp 默认端口22(使用SSH协议)，可以使用 -p 修改端口

  * ftp & sftp

    * ftp: 文件传输协议，1985年诞生

      * yum install ftp

      * 从公共的FTP服务下载文件
      * 从私有的FTP服务器上传或下载文件

    * sftp:  安全加密的ftp

*  rsync

  * remote synchronize 同步备份，远程同步文件
  * 同步同一台电脑或两台不同电脑上的两个文件(夹)的内容

* IP & hostname

  * host github.com
  * IP地址和主机名的解析是由DNS服务器完成的

* whois

  * whois github.com

* ifconfig

  * yum install net-tools

  * 列出网络接口

  * 旧版

    * eth0

      ```
      对应有线连接(对应有线网卡)，就是用网线来连接的上网(一般是RJ45网线)，如果你的电脑目前使用网线来上网，那就是在使用这个接口。eth是Ethernet的缩写，表示“以太网”。有些电脑可能有好几条网线连拉(好几个有线接口)，那么除了eth0外还有eth1,  eth2 等等 \
      ```

    * lo

      ```
      local Loppback，本地回环，对应一个虚拟网卡，可以看到它的ip是127.0.0.1，每个电脑都应该有这个接口，因为它对应着“连向自己的链接”。所有经由这个接口发送的东西都会回到你自己的电脑。
      ```

    * wlan0

      ```
      对应wifi无线连接(对应你的无线网卡)，如果有几块无线网卡，那么会看到wlan1, wlan2等等
      ```

    * 以上三种情形可以通过如下判断使用哪种连接方式

      ```
      RX packages: 4853
      TX packages: 4821
      ```

  * 新版

    ```
    新版用 systemd 替换掉了 initd 来引导系统
    其影响之一就是网络接口的命名方式变了
    ```

    * enp0s3

      ```
      en 代表以太网卡，p0s3代表PCI接口的物理位置(0,3)
      ```

    * lo

    * 关注

      ```
      网卡名称
      inet参数后面的ip地址
      ether参数后面的网卡物理地址(mac地址)
      RX、TX的接收数据包和发送数据包的个数，以及累计流量
      ```

* ip addr

  * 

* netstat

  * netstat -i  列出你电脑的所有网络接口的一些统计信息

    ```
    Iface             MTU    RX-OK RX-ERR RX-DRP RX-OVR    TX-OK TX-ERR TX-DRP TX-OVR Flg
    ens33            1500     5114      0      0 0          1682      0      0      0 BMRU
    lo              65536    26814      0      0 0         26814      0      0      0 LRU
    MTU: Maximum Transmission Unit，最大传输单元，指一种通信协议的某一层上能通过的最大数据包大小(单位字节)
    RX-DRP: 在此接口接收的包中丢弃的包数
    RX-OVR: 在此接口接收的包中，由于过速而丢失的数据包数
    ```

  * netstat -uta  列出所有开启的连接

    ```
    -u 显示UDP连接
    -t 显示TCP连接
    -a 不论连接的状态如何，都显示
    ```

  * netstat -lt  列出状态 listen 的连接

  

## shell

### shell的分类

```
不同的终端命令行对应不同的shell
```

* sh: Borune Shell， 目前所有shell的祖先

* Bash:  Borune Agin Shell, sh进阶版，比sh更优秀，是大多数Linux发行版和macOS操作系统的默认shell

* ksh、csh、tsh、zsh 。。。

  <img src="linux.assets/image-20210316061706764.png" alt="image-20210316061706764" style="zoom:33%;" />

### shell可以做什么

* shell是管理命令行的程序
* 以rc结尾的文件
  * 以rc结尾的多为配置文件
  * 里面包含了软件运行前会去读取并运行的那些初始化命令
* 

### 第一个shell

vim test.sh

```
#!/bin/bash

ls
```

* .sh是一种约定俗成的命名惯例

* 在写一个脚本时，第一要做的就是指定要使用哪种shell来解析/运行它，因为sh ksh bash等shell的语法有所不同

  * #! 被称作sha-bang或shebang，类unix操作系统会解析其后面字符，以其后面的字符解析命令
  * #!/bin/bash 如果不指定，默认以当前shell执行

* “#”  注释 

* ./test.sh  运行shell

* chmod +x test.sh  添加可执行权限

* 调试

  * bash -x testt.sh
  * 参数 -x 表示以调试模式运行
  * shell会把脚本文件运行的细节打印出来

* 创建自己的命令

  * PATH环境变量

    * PATH是Linux的一个系统变量，包含系统里所有可以被直接执行的程序的路径

    * echo $PATH

      ```
      /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin
      ```

    * cp test.sh /usr/bin 后可直接运行 test.sh

### 变量

* 定义变量

  ```
  vim variable.sh
  
  #!/bin/sh
  
  message='Hello World' # 等号两边不要加空格
  echo $message
  ```

  * 引号

    * 单引号：如果变量被包含在单引号里，变量不会被解析，会忽略被其括起来的所有字符

    * 双引号：双引号会忽略大多数特殊字符，但不包括：$  `  \

    * 反引号：要求shell执行被它括起来的内容

      ```
      #!/bin/bash
      
      message=`pwd`
      echo "You are in the directory $message"
      
      
      You are in the directory /root
      ```

* read

  * read命令读取到的文件(终端输入)会立即被储存在一个变量里

    ```
    #!/bin/bash
    read name
    echo "Hello $name !"
    ```

  * read命令可以一冷性给多个变量赋值

    ```
    #!/bin/bash
    read firstname lastname
    echo "Hello $firstname $lastname !"
    ```

  * -p 显示提示信息

    ```
    read -p 'Please enter your name : ' name
    ```

  * -n 限制字符数目

    ```
    read -p 'please enter your name (5 characters max) : ' -n 5 name
    ```

  * -t  限制用户的输入时间(单位：秒)

    ```
    read -p 'please enter the code to defuse the bomb (you have 5 seconds) : ' -t 5 code
    echo -e "\nBoom !" # -e 解析 \n
    ```

  * -s  隐藏输入内容(如密码)

* 数学运算

  * 在bash中，所有的变量都是字符串

  * let

    ```
    #!/bin/bash
    
    let "a = 2"
    let "b = 5"
    let "c = a + b"
    
    echo "c = $c"
    ```

  * bc  小数运算

* 环境变量

  * shell的环境变量可以被此种shell的任意脚本程序使用，可称为全局变量
  * env 显示环境变量
  * export 自定义环境变量

* 参数变量

  * ./variable.sh 参数1 参数2 参数3

    | 变量 | 含义               |
    | ---- | ------------------ |
    | $#   | 参数的数目         |
    | $0   | 被运行的脚本的名称 |
    | $1   | 第一个参数         |
    | $2   | 第二个参数         |
    | $N   | 第N个参数          |

    ```
    #!/bin/bash
    
    echo "you are executed $0, there are $# parameters"
    echo "the first parameter is $1"
    
    ./variable.sh param1 param2 param3 param4
    you are executed ./variable.sh, there are 4 parameters
    the first parameter is param1
    ```

  * shift 命令“挪移”参数，常用在循环中，使得参数一个接一个地被处理

### 数组

```
#!/bin/bash

array=('value0' 'value1' 'value2')
echo ${array[2]}
```

* 数组可以包含任意大小的元素数目，数组的编号不需要是连续的

### 条件

* if

  ```
  if [ 条件测试 ] 	 # 条件两边必须空一格
  then
  	做这个
  fi 					# 表示if语句结束
  ```

  * 在shell语言中，“等于”是用一个等号 = 来表示的，但是shell中用两个等号表示等于的判断也是可以的

    ```
    #!/bin/bash
    
    name1="lan1"
    name2="lan2"
    
    if [ $name1=$name2 ]
    then
    	echo "You two have the same name !"
    fi
    ```

  * else

    ```
    if [ 条件 ]
    then
    	做这个
    else
    	做那个
    fi
    ```

  * elseif

    ```
    if [ 条件1 ]
    then
    	做1
    elseif [ 条件2 ]
    then
    	做2
    elseif [ 条件3 ]
    then
    	做3
    else
    	做其它
    fi
    ```

* 判断字符串

  | 条件                 | 意义                 |
  | -------------------- | -------------------- |
  | $string1 = $string2  | =，shell大小写敏感   |
  | $string1 != $string2 | !=                   |
  | -z $string           | 字符串string是否为空 |
  | -n $string           | 字符串是否不为空     |

* 判断数字

  | 条件            | 意义 |
  | --------------- | ---- |
  | $num1 -eq $num2 | =    |
  | $num1 -ne $num2 | !=   |
  | $num1 -lt $num2 | <    |
  | $num1 -le $num2 | <=   |
  | $num1 -gt $num2 | >    |
  | $num1 -ge $num2 | >=   |

* 判断文件

  | 条件              | 意义                       |
  | ----------------- | -------------------------- |
  | -e $file          | 文件是否存在               |
  | -d $file          | 文件是否是一个目录         |
  | -f $file          | 文件是否是一个文件         |
  | -L $file          | 文件是否是一个符号链接文件 |
  | -r $file          | 文件是否可读               |
  | -w $file          | 文件是否可写               |
  | -x $file          | 文件是否可执行             |
  | $file1 -nt $file2 | 文件file1是否比file2更新   |
  | $file1 -ot $file2 | 文件file1是否比file2更旧   |

* && ||

  * && 逻辑与
  * || 逻辑或

* case

  ```
  case $1 in
  	"Matthew")
  		echo "Hello Matthew !"
  		;;   # 类似break
  	"Mark")
  		echo "Hello Mark !"
  		;;
  	"Luke")
  		echo "Hello Luke !"
  	"John")
  		echo "Hello John!"
  		;;
  	*)	# 相当于 else
  		echo "Sorry, I do not know you."
  		;;
  esac	# case结束
  ```

  * case中或用一个 ｜

### 循环

* while

  ```
  while [ 条件 ]
  do
  	做某些事
  done
  ```

  ```
  #!/bin/bash
  
  while [ -z $response ] || [ $response != 'yes' ]
  do
  	read -p "Say yes : " response		# 变量在此定义
  done	
  ```

* until

  ```
  【util 条件为真时跳出循环】
  
  until [ "$response" = 'yes' ]
  do
  	read -p 'Say yes : ' response
  done
  ```

* for

  ```
  for 变量 in '值1' '值2' '值3' ... '值n'
  do
  	做某些事
  done
  ```

  ```
  for i in `seq 1 10`
  do 
  	echo $i
  done
  ```



###　函数

* 函数定义

  ```
  函数名 () {	# 括号里不加任何参数
  	函数体
  }
  
  function 函数名 {
  	函数体
  }
  ```

  * **函数名后的括号不加任何参数**
  * 函数的完整定义必须在调用之前

* 传递参数

```
#!/bin/bash

print_something () {
	echo Hello $1
}

print_something Matthew
print_something Mark
```



* 变量作用范围

  * 默认地，一个变量是全局的
  * 要定义一个局部变量，需要用local关键字

* 返回值

  * 没有返回值
  * 可以返回状态，表示是否成功，使用return关键字

  ```
  #!/bin/bash
  
  print_sonething () {
  	echo Hello $1
  	return 1
  }
  
  print print_something Hike
  
  echo Return value of previous function is $?
  ```

  **$? 函数返回状态**

* 重载命令

  ```
  #!/bin/bash
  
  ls () {
  	command ls -lh  # command关键字用于重载
  }
  
  ls
  ```



### 统计练习



### systemd

```
systemd是Linux系统工具，用来启动守护进程。
systemd设计目标：为系统的启动和管理提供一套完整的解决方案
```



* 进程

  * 一个运行起来的程序称为进程 
  * 守护(daemon)进程
    * 不与任何终端关联，无论用户身份如何，都在后台运行
    * 这些进程的父进程是PID为1的进程，PID为1的进程只在系统关闭时才会被销毁
    * 守护进程的名字通常会在最后有一个d，表示daemon, 如 systemd, httpd, smbd等

* Linux初始化进程服务：systemd & systemv

  | 作用                               | systemd命令                             | systemv命令          |
  | ---------------------------------- | --------------------------------------- | -------------------- |
  | 启动服务                           | systemctl start toto                    | service toto start   |
  | 停止服务                           | systemctl stop toto                     | service toto stop    |
  | 重启服务                           | systemctl restart toto                  | service toto restart |
  | 查看服务状态                       | systemctl status toto                   | service toto status  |
  | 重载配置文件(不停止服务)           | systemctl reload toto                   | service toto reload  |
  | 开机自启动服务                     | systemctl enable toto                   | chkconfig toto on    |
  | 开机不自启动服务                   | systemctl disable toto                  | chkconfig toto off   |
  | 查看服务是否开机自动启动           | systemctl is-enabled toto               | chkconfig toto       |
  | 查看各个级别下服务的启动和禁用情况 | systemctl list-unit-file --type=service | chkconfig --list     |

  * systemd 的 PID 为 1
  * systemd并不是一个命令，它包含了一组命令，涉及到系统管理的方方面面
  * systemd是基于事件的，可以使进程【并行】启动(systemv是串行启动的，只有前一个进程启动完，才会启动后一个进程)
  * systemd可以重启因错误而停止的进程，管理任务计划、系统日志、外设等
  * **systemctl**
    * systemctl用于管理unit，unit泛指它可以操作的任何对象，包括：服务、挂载、外设等等
    * 每个unit都有一个配置文件，告诉systemd怎么启动这个Unit
  * systemv使用run level(运行级别)来管理不同的进程组，systemd用target替换了systemv的运行级别
    * 启动计算机的时候，需要大量的Unit，如果每一次启动，都要一一写明本次启动需要哪些Unit，显示非常不方便，systemd的解决方案就是Target
    * target是指“目标”，简单来说是多个unit构成的一个组
    * 
  * journalctl 管理日志
    * 默认地，journalctl按时间顺序显示由systemd管理的所有日志
  * systemd-analyze 系统启动耗时
    * systemd-analyze blame 每个unit启动耗时

* 安装Apache

  ```
  在Centos等RedHat一族中，Apache程序的名字叫 httpd。
  ```

  * yum install httpd
  * systemctl start httpd
  * systemctl stop httpd
  * 查看开放端口  firewall-cmd --list-ports
    * firewall-cmd -zone=public --add-port=80/tcp --permanent
    * firewall-cmd reload  重载配置的防火墙策略

### SELinux

* Security-Enchanced Linux 安全增强型Linux

* SELinux的“双重保险”

  | 模式                         | 含义                     |
  | ---------------------------- | ------------------------ |
  | 域限制(Domain Limitation)    | 对服务程序的功能进行限制 |
  | 安全上下文(Security Context) | 对文件资源的访问进行限制 |

* 防火墙和SELinux

  * 防火墙就像“防盗门”，用于抵御外部的危险
  * SELinux就像“保险柜”，用于保护内部的资源

* SELinux的三种配置模式

  **/etc/selinux/config**

  | 配置模式   | 含义                                         |
  | ---------- | -------------------------------------------- |
  | enforcing  | 强制启用安全策略模式，将拦截服务的不合法请求 |
  | permissive | 遇到服务越权访问时，只发出警告而不强制拦截   |
  | disabled   | 对于越权的行为不警告也不拦截                 |

* sestatus

  * 查看安全状态

* setenforce 0（1） 暂时

* getenforce

* semanage

  * 管理 SELinux，用于管理SELinux的策略

  * semanage [选项] [文件]

  * 安装：yum provides semanage

  * 参数

    | 参数 | 功能 |
    | ---- | ---- |
    | -l   | 查询 |
    | -a   | 添加 |
    | -m   | 修改 |
    | -d   | 删除 |

  * semanage fcontext -a -t httpd_sys_content_t /home/web/*


### IP分配

* DHCP动态分配IP
  * 动态主机配置协议，是一种基于UDP协议且仅限于在局域网内部使用的网络协议
  * 主要用于局域网环境或者存在较多办公设备的局域网环境中，为局域网内部的设备或网络供应商自动分配IP地址等参数
  * DHCP可以自动管理主机的IP地址、子网掩码、网关、DNS地址等参数
* 静态分配IP
  * gateway netmask dns

### 虚拟主机

* 独立服务器
* 虚拟主机
* VPS
* ECS
  * 弹性计算服务，也就是一般的**云服务器**，整合了计算、存储、网络，能够做到弹性伸缩的计算服务

### HTTPS

* HTTPS协议

  * 超文本传输**安全**协议，HTTP加上SSL/TLS协议构建的可以加密传输、身份认证的网络协议

  * SSL/TLS
    * SSL:  Secure Socket Layer，安全的套接字层；
    * TLS:  Transport Layer Security，传输层安全
    * TLS可以被看作SSL的新版本，在SSL3.0版的基础上，进行了改进，目前使用的其实都是TLS，不过沿用传统，一般也可以继续叫SSL
  * 为什么需要HTTPS
    * HTTP是明文信息，不安全
    * HTTPS可以确保所有经过服务器传输的数据包都是经过加密的
    * 使得假冒的服务器无法冒充真实的服务器
  * CA
    * Certificate Authorities，证书(公证)权威(机构)
    * CA用于为客户端确认所连接的网站的服务器提供的证书是否合法
    * 数字证书是经过CA认证的公钥，其内容不止包含公钥

  * **公钥、私钥、数字签名、数字证书详解**

    ```
    1、鲍勃有两把钥匙，一把是公钥，另一把是私钥
    2、鲍勃把公钥送给他的朋友们----帕蒂、道格、苏珊----每人一把
    3、苏珊要给鲍勃写一封保密的信。她写完后用鲍勃的公钥加密，就可以达到保密的效果
    4、鲍勃收信后，用私钥解密，就看到了信件内容。这里要强调的是，只要鲍勃的私钥不泄露，这封信就是安全的，即使落在别人手里，也无法解密
    5、鲍勃给苏珊回信，决定采用"数字签名"。他写完后先用Hash函数，生成信件的摘要（digest）
    6、然后，鲍勃使用私钥，对这个摘要加密，生成"数字签名"（signature）
    7、鲍勃将这个签名，附在信件下面，一起发给苏珊
    8、苏珊收信后，取下数字签名，用鲍勃的公钥解密，得到信件的摘要。由此证明，这封信确实是鲍勃发出的
    9、苏珊再对信件本身使用Hash函数，将得到的结果，与上一步得到的摘要进行对比。如果两者一致，就证明这封信未被修改过
    10、复杂的情况出现了。道格想欺骗苏珊，他偷偷使用了苏珊的电脑，用自己的公钥换走了鲍勃的公钥。此时，苏珊实际拥有的是道格的公钥，但是还以为这是鲍勃的公钥。因此，道格就可以冒充鲍勃，用自己的私钥做成"数字签名"，写信给苏珊，让苏珊用假的鲍勃公钥进行解密
    11、后来，苏珊感觉不对劲，发现自己无法确定公钥是否真的属于鲍勃。她想到了一个办法，要求鲍勃去找"证书中心"（certificate authority，简称CA），为公钥做认证。证书中心用自己的私钥，对鲍勃的公钥和一些相关信息一起加密，生成"数字证书"（Digital Certificate）
    12、鲍勃拿到数字证书以后，就可以放心了。以后再给苏珊写信，只要在签名的同时，再附上数字证书就行了
    13、苏珊收信后，用CA的公钥解开数字证书，就可以拿到鲍勃真实的公钥了，然后就能证明"数字签名"是否真的是鲍勃签的
    
    ```

    

  * 配置HTTPS

* 

