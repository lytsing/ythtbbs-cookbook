# 使用gdb调试FB2000

```
    1.故事背景
     
    之前发现一个很奇怪的bug,就是有些站务下不了站，下站的时候就会又
 
转到好友菜单，一开始只是一两个站务id遇到，出现也是随机的，很难调试，
 
曾经试着看代码跟踪，没看出什么问题。最近这个问题终于引发了全站性的
 
“灾难”。发现某天早上忽然web登陆不了，但telnet使用正常只是偶尔会
 
很卡，根据之前经验，先查看了一下bbshome下的两个日志文件：
 
bbs目录下的trace和usies, 问题就在这里，trace文件已超过2G,32位的机
 
器读超过2G的大文件会出现问题，但又有一个疑问出现了:为什么在10几天
 
内这个日志文件为什么会超过2G呢? more了一把，发现有两个站务id在下不
 
了站的时候一次写了大量的日志，满满的全是：
 
SYSOP 03月26日19:31:28 brc_save_all
SYSOP 03月26日19:31:28 brc_save_all
SYSOP 03月26日19:31:28 brc_save_all
 
数量大得可怕，一次写1w左右?为什么会这样呢?看了一下打出这个日志的地方
 
发现是brc_saveall()函数打出来的。好，查一下调用brc_saveall()的地方,
 
只有main.c跟bbs.c调用了。按分析，下站时出现这个问题应该在main.c的
 
u_exit()函数里面。但怎么看都看不出问题，就开始怀疑是信号引起的，后来
 
打了很多信息，也发现不是这个问题，那就怪了为什么程序会不停地在这个函数
 
里转呢?会调用多次这个函数呢?看了很多次代码都不得其解，最后实在没办法了
 
只好出动超级武器--gdb。
 
    2.武器出动
 
    想要用gdb调试程序要先在编译里加上-g或者-gdb
 
    1) 修改$bbshome/bbssrc/src下的Makefile文件
    将原来的 　 CFLAGS   = -O2 -Wunused -I../include
    修改成这样　CFLAGS   = -ggdb -Wunused -I../include
    
    2)在$bbshome/bbssrc/src下编译程序
    make clean;make
 
    3)在$bbshome/bbssrc/src直接执行bbsd
　　./bbsd 端口   如: ./bbsd 2008
    不要跟你系统现在运行的端口一样，这个可以分得清哪个是你要
　　抓的进程，等下就可以把它捉下来慢慢折磨它^_^
   
    4)用一个id登陆进去
    telnet ip 端口　　如：telnet 127.0.0.1 2008
    用不能退出的站务id登陆
 
    5)查一下你的进程号
    bbs@Fedora:~/bbssrc/src$ ps -ef|grep bbsd
    bbs       7096     1  0 01:53 ?        00:00:00 ./bbsd 2008
    bbs       7111  7096  0 01:55 ?        00:00:00 ./bbsd 2008
    7096的进程是我们的守护进行，因为他的父ID是init进程(1进程),我们
    要捉的是下面那个用户登陆进程(7111)。
 
    6)运行gdb调试bbsd
    bbs@Fedora:~/bbssrc/src$ gdb bbsd
    GNU gdb 6.1.1
    Copyright 2004 Free Software Foundation, Inc.
    GDB is free software, covered by the GNU General Public License, and you are
    welcome to change it and/or distribute copies of it under certain conditions.
    Type "show copying" to see the conditions.
    There is absolutely no warranty for GDB.  Type "show warranty" for details.
    This GDB was configured as "i486-slackware-linux"...Using host 
    libthread_db      library "/lib/libthread_db.so.1".
    (gdb) list
    504         register time_t uptime;
    505         int readset;
    506         int value;
    507         struct timeval tv;
    508
    509        /* --------------------------------------------------- */
    510        /* setup standalone                                    */
    511        /* --------------------------------------------------- */
    512
    513         start_daemon();
    (gdb)
   
    可以看到，当我们试着执行list的时候，可以看到程序的代码了。
　  7)现在我们可以执行attach+进程号把进程捉下来慢慢看了
    (gdb) attach 7111
    Attaching to program: /bbs/bbssrc/src/bbsd, process 7111
    Reading symbols from /lib/libtermcap.so.2...done.
    Loaded symbols for /lib/libtermcap.so.2
    Reading symbols from /lib/libdl.so.2...done.
    Loaded symbols for /lib/libdl.so.2
    Reading symbols from /lib/libnsl.so.1...done.
    Loaded symbols for /lib/libnsl.so.1
    Reading symbols from /lib/libcrypt.so.1...done.
    Loaded symbols for /lib/libcrypt.so.1
    Reading symbols from /lib/libc.so.6...done.
    Loaded symbols for /lib/libc.so.6
    Reading symbols from /lib/ld-linux.so.2...done.
    Loaded symbols for /lib/ld-linux.so.2
    Reading symbols from /lib/libnss_files.so.2...done.
    Loaded symbols for /lib/libnss_files.so.2
    0x40143c62 in select () from /lib/libc.so.6
    (gdb) list
    514
    515         (void) signal(SIGCHLD, reapchild);
    516         (void) signal(SIGTERM, close_daemon);
    517
    518
    519        /* --------------------------------------------------- */
    520        /* port binding                                        */
    521        /* --------------------------------------------------- */
    522
    523         xsin.sin_family = AF_INET;
　 
     当我们再执行list的时候可以看到程序执行到当前的代码,这时你登陆
进去的id已经动不了了，因为进行一attach就停掉了,释放时可以执行detach。
   
    8)设置断点break(b)
    之前我们分析bug出现在main.c的u_exit()函数，所以我们可以执行以下
　　命令：b u_exit
  
    (gdb) b u_exit
    Breakpoint 1 at 0x807b010: file main.c, line 224.
   
    显示断点的位置。当程序执行到这个函数的时候就会停止下来，这时你
　　 可以对变量，栈等变量进行查看，对函数调用进行查看。
 
　　9)让程序继续执行continue(c)
    (gdb) c       
    Continuing.
       
    这里程序继续执行了，你登陆的id也可以活动了，当执行到8)我们设置
　　的断点的时候才会再次停止下来。
 
　　10)程序执行到断点
    (gdb) c       
    Continuing.
    Breakpoint 1, u_exit () at main.c:224
    224         if (strcmp(currentuser.userid, "guest") != 0)
    下站时执行到u_exit()函数，所以程序暂停了。
 
　　11)让程序单步往下执行next(n) / step(s)
    (gdb) n
    225             brc_saveall();          // 保存所有阅读记录
    (gdb) s
    brc_saveall () at boards.c:1172
    1172        report("brc_save_all");
    (gdb)
    next是跳过函数往下执行，也就是把函数当成一条语句。
　　step是跳进函数内部再执行。
　　上面当我们执行一次n后发现接下来是brc_saveall()函数，因为我们
　　的问题出现在这个函数里面，我们需要进这个函数内部分析，所以我
　　们执行了s,跳进函数内部去一步一步执行。
 
    12)查看变量
　　print(p) +变量名　可以打印出变量当前的值
　　如果想打印数组，也可以直接 p + 数组名，但有可以看起来不是很
　　舒服，这时我们可以把一个开关打开，看起来就会舒服很多.
    set print pretty on
    再打一个变量就会漂亮得多了。
   (gdb) p brc_read_num
    $1 = 2
   (gdb) p brc_read
    $2 = {{bid = 1, list = {1158002774 <repeats 60 times>}, num = 60, changed = 0 '\0', readpos = 0}, {bid = 5, list = {1158013150, 1,
      0 <repeats 58 times>}, num = 2, changed = 0 '\0', readpos = 0}, {bid = 0, list = {0 <repeats 60 times>}, num = 0,
    changed = 0 '\0', readpos = 0} <repeats 248 times>}
   (gdb) set print pretty on
   (gdb) p brc_read        
    $3 = {{
        bid = 1,
        list = {1158002774 <repeats 60 times>},
        num = 60,
        changed = 0 '\0',
        readpos = 0
      }, {
        bid = 5,
        list = {1158013150, 1, 0 <repeats 58 times>},
        num = 2,
        changed = 0 '\0',
        readpos = 0
      }, {
        bid = 0,
        list = {0 <repeats 60 times>},
        num = 0,
        changed = 0 '\0',
        readpos = 0
     } <repeats 248 times>}
 
    13)发现问题
　　一直往下next的时候，发现在执行
    1183               ptr =
    1184                    brc_putrecord(ptr, b_name, brc_read[i].num,
    1185                                  brc_read[i].list);
　　函数时,会收到memcpy内存溢出的信号。
　　
    14)s进函数brc_putrecord()
      1158            memcpy(ptr, list, num * sizeof(int));
      看到有这样一句。在memcpy的时候溢出了。
 
    15)可以用bt看一下函数堆栈
(gdb) bt
#0  brc_putrecord (ptr=0xbfff2ed0 "junk", name=0x403d4430 "junk", num=2, list=0x8194244) at boards.c:1156
#1  0x08067319 in brc_saveall () at boards.c:1183
#2  0x0807b02d in u_exit () at main.c:225
#3  0x0805c92a in Q_Goodbye () at bbs.c:3451
#4  0x0805cda6 in Goodbye () at bbs.c:3557
#5  0x0806ca4a in domenu (menu_name=0x80bcb32 "TOPMENU") at comm_lists.c:704
#6  0x0807d221 in start_client () at main.c:1281
#7  0x0805f180 in main (argc=2, argv=0xbffff7d4) at bbsd.c:651
    　
    这是函数调用的顺序。
 
    我print了一下该用户的brc_read数组，确实发现有一个的数据相当异常。
 
    至此发现了问题的所在了，是brc_read数组数据的异常引起的，回到主站
 
　  把该用户下面的.boardbrc删除掉，可以暂时让用户正常下站，因为删除了
 
　　 这个记录用户未读文章的记录文件，系统会自动生成一个，但会让该用户
 
　　 的所有文章标记为未读。　
　　
　　　如：$bbshome/bbs/home/S/SYSOP/.boardbrc
    
     分析：
 
　　　问题是找到了，但还有几个未解决问题：
 
　　　1)现在删除.boardbrc只是治标不治本，因为当我删除某些站务的这个
 
　　　文件后，用户是能正常下站了，可是过了几天又不行了，问题当然是
 
　　　出在生成这个文件的程序，或者是某些版面的配置有问题。
 
　  　经过这次程序在网上查了一下资料，看到先辈们的一些讨论也对这个
 
　　　结构体有了一定的了解，下次再细说一下。
 
      2)当程序出到内存溢出的信号后程序会自动将这个函数再执行一次，
 
　　　加了-ggdb后，只会执行一次，但如果加了-O2会执行n次，搞不明白
 
      3)到底为什么会溢出?是越界了，还是?因为是站务个人的id,不能拿
 
　　　他们的id直接来调试，有空的时候再把.boardbrc文件转到测试站上
 
　　　再试一把。
 
      4)为什么只有站务会出现这个问题，其他人不会?最大的可能是引发
 
　　　这个bug的版面是内站版面，也可能是某些站务的习惯引起bug的爆发,
 
      也可能是之前有对一些版面做特殊处理所引起的。
 
      5)如果暂时找不到bug的根原，是否在出现内存溢出信号时直接返回，
 
　　　exit掉进程，影响的是当前用户的未读文章记录，但不至于影响整
 
　　　个站的正常运行。
 
       6)目前两个不能下站的站务id,表现出是不同的现象，有一个会不
 
　　　 停地写AXXED记录，其他一个不会，有可能引发的原因是不同的?
 
      那就更麻烦了。恩恩嗯嗯嗯。
    

```

```
发信人: Czz@feeling-NOsmthSPAM-org (看片看片), 信区: BBS_DEV 
标  题: Re: 如何调试FB2000？ 
发信站: 温馨小屋 (Sun Apr 27 07:39:22 2003) 
转信站: SCU!news.neu.edu.cn!news.bjsing.net!news.feeling.smth.org!Feeling 
 
                    GDB调试Firebird源代码的方法(bbsd) 
     
                                            by flyriver@happynet.org 
   
本文是我为 HAPPY BBS 系统进行维护期间一些心得的总结。由于  
HAPPY 使用的原始版本是 FireBird-3.0-19990513-SNAP，因此如  
果你使用的是 FB2000 的话，有些地方可能有所差别，但差别应该  
不会太大，请自行修正。  
   
调试环境  
    服务器(Linux, 运行 FB 系统)  
    客户端(Win2k, SecureCRT, S-Term)  
   
首先用 SecureCRT 打开一个终端连到服务器上，设为终端1。  
     
1. 修改 bbsd.c， 
     
首先找到 main() 函数，在 accept() 一个 client 之后的那个 fork() 部分，修改 
成如下代码：  
     
        pid = fork();  
     
        if (!pid)  
        {  
#ifdef BBS_GDB  
            {  
                char debug_file[128] = "/tmp/start";  
                struct stat fs;  
   
                while (stat(debug_file, &fs) < 0)  
                    sleep(1);  
            }  
#endif  
            //...  
        }  
   
2. 然后修改主Makefile文件(在src目录外面的那个) 
     
分别找到  
CFLAGS   = -O2 -Wunused -I../include  
CSIE_DEF = -DSHOW_IDLE_TIME -DWITHOUT_CHROOT  
   
    把他们注释掉，并改为下面的：  
#CFLAGS   = -O2 -Wunused -I../include                   # 注视掉原来的  
CFLAGS   = -ggdb -Wunused -I../include                  # 加上调试选项  
#CSIE_DEF = -DSHOW_IDLE_TIME -DWITHOUT_CHROOT           # 注视掉原来的  
CSIE_DEF = -DBBS_GDB -DSHOW_IDLE_TIME -DWITHOUT_CHROOT  # 定义BBS_GDB宏  
   
3. 重新编译源代码 
     
   make clean  
   make  
   
4. 启用一个调试进程 
     
   # cd src  
   # ./bbsd 9000  
   
5. 运行 gdb 调试器 
     
   # gdb bbsd  
GNU gdb Red Hat Linux 7.x (5.0rh-12)  
Copyright 2001 Free Software Foundation, Inc.  
GDB is free software, covered by the GNU General Public License, and you are  
welcome to change it and/or distribute copies of it under certain conditions.  
Type "show copying" to see the conditions.  
There is absolutely no warranty for GDB.  Type "show warranty" for details.  
This GDB was configured as "i386-redhat-linux"...  
(gdb) b start_client  
Breakpoint 1 at 0x805fa35: file main.c, line 1056.  
   
6. 连接到调试端口 
     
再用 SecureCRT 打开另一个终端，设为终端2，然后用 S-Term 连到服务器的 9000 调试  
端口，此时会发现 S-Term 停住了，而不是平常的 FB 登录界面，这就对了。  
在终端2 中输入运行  
    $ ps aux  
bbs       1924  0.0  0.2  1780  648 ?        S    09:33   0:00 bbsd ( 9000  
bbs       1929  0.0  0.2  1780  648 ?        S    09:34   0:00 bbsd ( 9000  
   
可以发现，1929 号进程就是响应 S-Term 的 bbsd 子进程。  
   
7. attach bbsd 子进程 
     
   切回到终端1 的 gdb 界面  
(gdb) attach 1929  
Attaching to program: /home/bbssrc/src/bbsd, process 1929  
Reading symbols from /lib/libtermcap.so.2...done.  
Loaded symbols for /lib/libtermcap.so.2  
Reading symbols from /lib/libdl.so.2...done.  
Loaded symbols for /lib/libdl.so.2  
Reading symbols from /lib/libc.so.6...done.  
Loaded symbols for /lib/libc.so.6  
Reading symbols from /lib/ld-linux.so.2...done.  
Loaded symbols for /lib/ld-linux.so.2  
0x400d6cb1 in __libc_nanosleep () from /lib/libc.so.6  
   
    可以看到此时进程运行到第 1 步中的新增的循环代码部分。  
   
8. 让 bbsd 子进程脱离循环 
     
   切回终端2，输入  
   $ touch /tmp/start  
   
9. 调试开始 
     
   切回终端1 的 gdb 界面  
(gdb) c  
Continuing.  
   
Breakpoint 1, start_client () at main.c:1056  
1056            chdir("/home/bbs");  
(gdb) n  
1061            load_sysconf();  
(gdb) n  
1070            system_init();  
     
(gdb)  
....  
   
   
说明一下，上面的有些步骤其实是可以省的，这里的写法是为了简单易用。 
这种调试方法有一个很好的地方就是 FB 的用户界面与 gdb 调试界面分开 
了，因而可以极大的方便 FB 的开发。不要再固执地使用 prints() 这种 
低效率的方法了，把 gdb 这把利器磨得更锋利才是正途。 
 
-- 
 
※ 来源:·温馨小屋 bbs.feeling.smth.org·[FROM: localhost] 
```