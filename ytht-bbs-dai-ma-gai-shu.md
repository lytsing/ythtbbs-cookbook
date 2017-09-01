# YTHT BBS 代码概述

## 代码目录

```
➜ /Users/lysting/work/ytht git:(master)>tree -L 1
.
├── AUTHORS
├── Bindent
├── COPYING
├── ChangeLog
├── FAQ
├── INSTALL
├── INSTALL.web
├── Installbbs.sh.in
├── LICENSE
├── Makefile.Base.in
├── Makefile.am
├── Makerule.base
├── NEWS
├── README
├── README.md
├── Version.Info
├── atthttpd
├── bbshome
├── configure.ac
├── cthulhu
├── devweb
├── doc
├── ebbs
├── games
├── include
├── inn
├── innbbsd
├── libythtbbs
├── local_utl
├── mail2bbs
├── makedist.sh
├── nju09
├── rzsz
├── site
├── smth_sshbbsd
├── software
├── src
├── szm
├── tests
├── wwwtools
├── yftpd
├── yspam
└── ythtlib

25 directories, 18 files
```

## 目录简要说明

### bbshome

安装的时候会将这个目录下面的文件复制到bbs用户的家目录下面。里面保存着配置信息文件目录etc，帮助信息文件目录help等。

### software

主要包括 ghthash 、fastcgi 两个库无依赖关系

### ythtlib

用于生成 YTHT BBS的一个静态库 /home/bbs/bin/libytht.a 无依赖关系

### libythtbbs

用于生成 YTHT BBS的一个静态库 /home/bbs/bin/libythtbbs.a 无依赖关系

### src

BBS 的 Telnet 登陆部分依赖于 ythtlib 和 libythtbbs

### local_utl

BBS 的本地实用工具程序，就是utility。精彩话题的显示、生成导读、版务考勤、自动解封、系统负载、有关数据统计、备份等实用script，还有各种log程序。依赖于 ythtlib 和 libythtbbs


### nju09

BBS 的Web 登陆部分依赖于 ythtlib 和 libythtbbs

### innbbsd

BBS 的转信部分依赖于 ythtlib 和 libythtbbs

### yftpd

前身是 bftpd，为 bbs 量身打造的 ftp，用来上传和下载文件，比如版主管理进版页面使用，精华区的下载。部分依赖于 ythtlib 和 libythtbbs


### atthttpd

BBS 的 Web 附件服务器部分依赖于 ythtlib 和 libythtbbs

### smth_sshbbsd

顾名思义，就是用水木清华BBS代码的ssh部分，提供ssh访问。部分依赖于 ythtlib 、libythtbbs 和 src 目录

### devweb
YTHT dev主页。make install会安装到/home/bbs/ftphome/root/boards/BBSDev/html

ytht已成为历史,现在已经没有实际作用了，有兴趣可以看一下，个人认为没有必要安装。

### ebbs
棋牌中心，ylsdd 作品
INSTALL:
```
$ gcc -o chess chessd.c
$ gcc-o chc chess.c
```
        
然后将生成的chessd和chc拷贝到($BBSHOME)/bin，运行./chessd& 启动后台程序即可。
