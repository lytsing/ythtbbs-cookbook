# YTHT特别服务安装指南

```
发信人: tnds (拖泥带水buct.ytht.net&buct.mysmth.org), 信区: BBSMan_Dev
标  题: ytht特别服务程序安装指南(转自BLOG)
发信站: BBS 水木清华站 (Fri Dec 17 20:37:10 2004), 转信

【以下文章转自 tnds 的BLOG：拖泥带水】
BLOG地址：http://www.smth.edu.cn/pc/index.php?id=tnds
日志地址：http://www.smth.edu.cn/pc/pccon.php?id=1218&nid=111622&s=all


    ytht特别服务程序安装指南

本文所提到的软件包除特殊注明外，均可从以下两个压缩包中获得

http://bbs.zzu.edu.cn/ytht.rar

http://bbs.zzu.edu.cn/ytht1.rar

部分变量说明

($BBSHOME):BBS安装主目录，通常为/home/bbs

($BBSSRC):BBS源码目录，通常为/home/bbssrc

($WWWHOME):web环境主目录，通常为/var/www

以下内容一般以cygwin为例说明，对于其他系统，有时需要更改扩展名，例如linux系统下可执行文件没有.exe扩展名，自行修改即可。

一、翻查字典(cdict)安装

1.将cdict-1.0-1.i386.rpm(在ytht1.rar中)解压到用户目录，执行

rpm -ih cdict-1.0-1.i386.rpm (for redhat/cygwin等）

或将cdict-1.0-1.i386.deb解压到用户目录，执行

dpkg -i cdict_0.1-3_i386.deb (for debian)

2.cp ($BBSSRC)/local_utl/shellscript4bbsuser/cdict2.sh ($BBSHOME)/bin/cdict.sh

二、科技词典(ncce)安装

注：由于cygwin缺少newt的支持，由此科技词典在cygwin环境下只能用于web

1.将nccedict.zip(在ytht1.rar中)的CE.IDX、CE.LIB、EC.IDX和EC.LIB复制到($BBSHOME)/etc/ncce

注：如果不是标准安装在/home/bbs目录，同时需要修改libncce.c中#define DICPATH 将其指向实际目录。

2.cd ($BBSSRC)/games/ncce
   make
   cp cgincce.exe ($WWWHOME)/cgi-bin
   cp ncce.exe ($BBSHOME)/bin

三、背单词安装

1.背单词的词库是我爱背单词4.0(一说2000）版的词库，但现在网上流传的多为2001及之后版本，我曾在maze上找到一个，但词库似乎仍与糊涂的对不上号。

2.背单词同样需要newt支持，因此在cygwin环境暂时无法使用。

3.如果有词库，将其拷贝至recite.c中所定义的libdir,同时要建立相对应的logdir

4.cd ($BBSSRC)/games/recite
   make
   cp recite.exe ($BBSHOME)/bin

四、推箱子(worker)安装

1.如果不是安装在标准的/home/bbs目录，需要修改worker.c 94行的map.dat为其实际目标路径。

2.cd ($BBSSRC)/games/worker
   make worker
   cp worker.exe ($BBSHOME)/bin
   mkdir ($BBSHOME)/etc/worker
   cp map.dat ($BBSHOME)/etc/worker

五、俄罗斯方块(Tetris)安装

mkdir ($BBSHOME)/etc/tetris
cd ($BBSSRC)/games/tetris
make
cp tetris.exe ($BBSHOME)/bin

六、扫雷与感应式扫雷安装

1.若bbs未安装在标准目录，需要分别修改winmine.c和winmine2.c中的winminepath和winmine2path使其指向实际的目录。

2.mkdir ($BBSHOME)/etc/winmine
  mkdir ($BBSHOME)/etc/winmine2
  cd ($BBSSRC)/games/winmine
  make winmine
  make winmine2
  cp winmine.exe ($BBSHOME)/bin
  cp winmine2.exe ($BBSHOME)/bin

七、打字练习(TT)安装

1.在cygwin下的time函数似乎有问题，造成打字游戏无法正常运行.

2.若bbs未安装在标准目录，同样需要修改TT.c中的recordfile定义，使其指向实际的目录

2.mkdir ($BBSHOME)/etc/tt
   make tt
   cp tt.exe ($BBSHOME)/bin


八、棋牌中心(Chess)的安装

不详，欢迎高手补充

九、数字运算(Quickcalc)的安装

1.确认安装如下软件包，redhat、cygwin可用rpm -qa|grep [软件包名] 查询：

libreadline4
libreadline4-dev
libreadline
flex

2.将quickcalc-1.26.tgz解压到，并将($BBSSRC)/games/quickcalc-1.26-ythtpatch也复制到该目录。

3.patch<./quickcalc-1.26-ythtpatch
   make
   cp qc.exe ($BBSHOME)/bin

十、Freeip的安装

1.cd ($BBSSRC)/games/bbsfreeip
  make getip
会下载最新的纯真数据库（这个同时也可以用于fterm和QQ),如果你有这个数据库的话，可以直接将qqwry.dat拷贝至($BBSSRC)/games/bbsfreeip并跳过第2步

2.对于在windows安装的,可以直接用winrar将qqwry.dat解压出来。
对于在linux下安装的，就比较麻烦得去
 http://www.rarlab.com/rar/rarlinux-3.3.0.tar.gz
下载unrar并解压。

3.make qqip
./qqip
生成ip_arrange.txt

4.make sortip
   ./sortip
生成ip_arrange_sort.txt

5. make ipnums.h
生成定义

6. $make

7.$make install

在cygwin下可能会出错，如果出现什么the same 之类的error,可以直接用windows的资源管理器进行复制，具体的路径就是：
把freeip.exe拷贝到($BBSHOME)/bin
把ip_arrange_sort.txt拷贝到($BBSHOME)/etc
把cgifreeip.exe拷贝到($WWWHOME)/cgi-bin

8.如果安装出现错误，可以考虑根据makefile中的内容单独make

9.我在配置最新代码时出现了错误，不知道是不是数据库文件过大的问题，如果出现这个问题，可以考虑换用11月份左右的数据库。

十一、  元素周期表

cd ($BBSSRC)/wwwtools/periodic
mkdir ($WWWHOME)/html/periodic
cp * ($WWWHOME)/html/periodic

十二、LINUX手册

1.由于这个程序是调用的linux的内置手册，因此在cygwin下只能看到封面而不能看到内容

2.cd ($BBSSRC)/games/cgiman
   make cgiman
   cp cgiman.exe ($WWWHOME)/cgi-bin

十三、数学公式支持

1.我没有能在cygwin下调通。

2.cd ($BBSSRC)/games/tex/itex
   make
   cp itex2MML.exe ($BBSHOME)/bin
   cp math2mathplayer.exe ($BBSHOME)/bin

3.测试是否安装成功可参考此文

http://bbs.tju.edu.cn/itexintro.html

如果输入了一个数学公式，并且选择了使用tex数学公式，并且你也安装了相关的软件，应该能够正常显示。



--

※ 来源:·BBS 水木清华站 http://smth.org·[FROM: 219.225.4.*]
```
