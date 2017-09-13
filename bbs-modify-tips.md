# 关于BBS代码安装使用修改的几个tips 

```
发信人: lepton.bbs@ytht.net (谁给糊涂一点钱), 信区: BBSDev 
标  题: 关于BBS代码安装使用修改的几个tips 
发信站: YTHT (Wed Aug  6 21:51:43 2003) 
转信站: WHU!news.tiaozhan.com!news.happynet.org!YTHT 
  
copyleft lepton@ytht.org 
  
希望本文能给初接触BBS,打算好好折腾折腾这个东西的朋友一点小小的帮助.如果是 
打算只是随便弄来玩玩的,就没有必要看本文档了,如果想偷懒的,只是想有个bbs能 
跑的,请联系本文作者购买商业支持.:) 
  
tips 1: 
        linux发行版本和文件系统的选择. 
        首先,对初学者来说,redhat就是不错的选择,一来安装比较方便,二来也比较 
容易找到.我个人觉得,如果你是希望一次装好一个稳定的操作系统,那么建议用redhat 
7.3,作为服务器来说,没有必要使用redhat 8.0或者redhat 9.0,实际上,这后面两个版 
本主要是针对桌面用户的,也就是说私人拿来跑图形界面自己玩不错,但是开服务器稳定 
性也许有问题.这里我只举一个具体的例子,高版本的rpm包管理器就有bug,经常会死锁, 
但是redhat 7.3的rpm就没有这个问题. 
        当然,其实这个问题并没有那么严重,如果你一定要用redhat 8.0或者9.0,问题 
也不大,因为Linux一旦安装好之后,从内核到应用程序都是可以更换的,最多将来发现哪里 
有问题,再更换哪个部分得了.不过我还是建议你安装redhat 7.3 
        就个人使用的感觉而言,开bbs的文件系统用reiserfs不错.当然不开bbs,用reiserfs 
作为自己文件系统也不错.不过redhat在安装程序上面做了手脚,缺省情形你在安装的时候是 
没法选择reiserfs作为自己的文件系统的.不过redhat还好没有那么绝情,他留下了一个小小的 
后门可以在安装时候打开reiserfs支持.在安装的时候的boot:的提示符下面,输入带有reiserfs 
的字符串引导安装过程后,在安装时候就可以选择reiserfs了.比如我自己,希望用文本界面安装, 
那么我就习惯输入"text reiserfs", 
  
tips 2: 
        阅读编辑代码的工具的选择. 
        很多小问题,如果自己能看看代码,那么问题就迎刃而解了.不过代码这么多,从哪里 
看起呢.看着看着,看到这么多函数,都是什么意思啊. 
        首先,建议你看看这篇东西,也可试验一下: 
        http://www.tldp.org/HOWTO/C-editing-with-VIM-HOWTO/ 
        这里提到了ctags make 和vim 协同工作的一些小tips. 
        另外,需要指出的是,你应该查询一下自己装的是否是增强版本的vim,使用命令rpm -qa|grep vim 
可以看到自己装了哪些vim的包,我一般习惯安装vim-enhanced这个包,而不安装vim-minimal 
  
tips 3: 
        怎么对付异常断线. 
        其实如果这个异常断线容易重复,那么修改起来就不是难事,方法如下,首先,修改Makefile, 确认 
自己的程序编译时候加了-g参数,这样编译出来的东西里面才有调试信息,如果是telnet下面的bbs,那么可以 
先telnet上,不要执行会引起断线的操作,然后用ps ax找出这个bbs进程的pid,然后运行gdb bbs,里面输入 
attach <pid of your bbs program>,然后输入continue,然后再在bbs窗口进行一些操作,触发断线的条件, 
这时回到gdb窗口,就可以看到是哪里出了问题. 
        www程序则可以这样调试: 
        cgi或者fastcgi,执行的原理是这样的,web服务器会设置好一些环境变量,然后www程序是从这些环境 
变量里面得到很多信息,比如用户来源ip,用户访问的url....,所以在命令行下面,只要先export相应的环境变量, 
就可以直接执行www程序看看输出是什么. 
        比如ytht的www程序,就可以这样在命令行下面测试是否能正常工作: 
        export SCRIPT_URL=/ 
        ./www 
        如果需要用gdb调试,那么用gdb www运行就可以了 
        有的时候,如果程序异常产生了core,那么也可以用gdb做分析,命令是 
        gdb -c core <program name>                
  
一些废话: 
对于系统调用和libc的库函数,可以多用man来查看说明. 
对于Makefile的写法,可以info make来查看说明. 
cgi apache之类的东西,用google可以搜索到很多简介,可以随便看看,有个概念. 
-- 
1.收款人姓名: 吴涛                     2.户名: 吴涛 
  一卡通帐号: 0010 29437501             帐号: 60 1000089 2 00906688 (活期存折) 
  开户行: 招商银行北京市分行            开户局: 北京苏州街邮电局 (邮编 100089) 
3.收款人姓名: 吴涛                     4.收款人姓名: 吴涛 
  帐号: 4367 4200 1262 0125 407 (龙卡)  帐号: 0200216401009685461 (活期存折) 
  汇入地名称: 中国建设银行北京市分行    汇入地名称: 中国工商银行北京市分行 
  
※ 来源:．一塌糊涂 BBS ytht.net．[FROM: 219.238.245.123] 
```