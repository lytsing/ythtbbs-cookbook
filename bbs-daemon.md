# 谈谈daemon

```
发信人: zhch.bbs@bbs.nju.edu.cn, 原信区: BBSDev
标  题: 谈谈daemon
发信站: 南京大学小百合站 (Sat Jul  7 13:11:38 2001), 站内信件

daemon(守护进程)是与用户进程相并列的一类进程. 一般的linux编程书籍对
daemon均有较详细的介绍. 也可参阅本站post版第221文<<也谈编写Unix的
daemon程序>>.

一个一般的用户进程要转换成一个守护进程需要进行一系列的处理. 从实用角度
来说, daemon进程和普通进程并没有本质差异, 成为daemon的每一个处理步骤都
是可省的. 当然, 每个步骤有其特定的作用, 省去后会带来相应的问题. 实际编
程时可自行掌握.

下面结合bbs中的情况, 对此做一些讨论.

一个进程成为daemon需要进行以下的步骤: (net server型daemon)

1, fork(), 变成后台进程.
2, setsid(), 新建一个session. 脱离终端.
3, signal(SIGHUP, SIG_IGN) 然后再fork(), 保证不是session的头. 这里signal
的作用是防止session头退出时被杀.
4, 把自己的pid记录到某个.pid文件中. 便于重起.
5, 改变当前目录到/或者/tmp. 以保证不占用目录.
6, 设置umask(). 让子进程fopen的时候继承.
7, 关闭所有fd. 以免被子进程继承.
8, 忽略不必要的signal, 防止被误杀.
9, 用dup或dup2, 重定义fd 0,1,2, 方便子进程使用stdio函数.
10, 建立日志文件. 记录重要信息.
11, 处理子进程死亡, 防止zombie产生. 这有许多方法, 最常见的是编写SIGCHLD处
理函数.
12, bind前必须setsockopt为SOL_SOCKET. 否则下次启动时必须等待几分钟才能重起.
13, setuid. 当必须由root启动(如需要bind低端口), bind后必须改变uid以保证安全.
14, 设置环境变量. 供子进程使用.

这些步骤中:
第1步fork一般不省略, 若省略, 也可以用 xxx & 或者nohup xxx &来启动进程, 效果
差不多; 或者就一直占用着终端做服务, 问题也不大.
第2步setsid常省略, 一般问题不大.
第3步如果setsid省略了, 则也应该省略. 如果setsid没省, 则第3步不能省略, 否则就
适得其反了: daemon退出时所有子进程都会退出, bbs里面这样的话update会很不方便.
apache的httpd这里似乎就有问题, 把httpd首进程杀了所有子进程都会死. (没仔细看)
第4步主要是重起方便, httpd, bbsd等一般都是这么做的. 省略了问题也不大, ps看
启动时间就行了.
第5步最好不要这么做, bbs里面chdir(BBSHOME)更方便一些, 占用目录问题不大.
第6步正常情况下省略问题也不大, 就是缺省的umask.
第7步bbs里面最好不要省略, 全部关闭比较好, 否则很占fd资源.
第8步一般不省略, 否则很容易死掉. FB3.0的bbsd里面应该加强一下.
第9步一般不省略, stdio使用起来很方便, 当然省略了直接用fd read,write问题也不
大. 只是稍麻烦一点.
第10步完善的daemon一般都有, 方便一些.
第11步在linux中, 第8步的处理就已经可以避免zombie了, 但为了兼容, 最好再专门处
理一下.
第12步一般不省略, bbs中的chatd就有这个问题, 重起很困难. 加上以后就好了.
第13步bbs中一般不省略, 除安全性外, root身份运行bbs会产生很多问题.
第14步是daemon向子进程传递信息的一种好方法, 如httpd向cgi传递一些参数, bbsd
传递fromhost都可以这么做.

--

zhch.bbs@bbs.nju.edu.cn

※ 来源:．南京大学小百合站 bbs.nju.edu.cn．[FROM: dsl.nju.edu.cn]
```