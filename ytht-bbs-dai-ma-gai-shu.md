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

## 简要说明

### software

主要包括ghthash 、fastcgi 两个库无依赖关系

### ythtlib

用于生成YTHT BBS的一个静态库 /home/bbs/bin/libytht.a 无依赖关系

### libythtbbs

用于生成YTHT BBS的一个静态库 /home/bbs/bin/libythtbbs.a 无依赖关系

### src

BBS 的Telnet登陆部分依赖于 ythtlib 和 libythtbbs

### local_utl

BBS 的本地实用工具程序依赖于 ythtlib 和 libythtbbs

### nju09

BBS 的Web 登陆部分依赖于 ythtlib 和 libythtbbs

### innbbsd

BBS 的转信部分依赖于 ythtlib 和 libythtbbs

### yftpd

BBS 的 Ftp 服务器部分依赖于 ythtlib 和 libythtbbs

### atthttpd

BBS 的Web 附件服务器部分依赖于 ythtlib 和 libythtbbs

### smth_sshbbsd

BBS 的ssh 登陆部分依赖于 ythtlib ，libythtbbs 和 src 目录
