# 精华区打包简介


源程序:

```
[BBSSRC]/local_utl/dl/
                      |_ Makefile
                      |_ an.c           对单个版面打包
                      |_ an.h           设置精华区打包存放
                      |_ anall.c        全站打包
```
                      
Makefile
======== 
```
BASEPATH = ../..
include $(BASEPATH)/Makefile.Base

BIN:= an anall
.PHONY:all install clean
all:$(BIN)
an: an.c
        $(CC) -o $@ $(CFLAGS) $< $(BBSLIB)
anall:anall.c
        $(CC) -o $@ $< $(CFLAGS) $(BBSLIB)

all: $(BIN)
install: all
        cp $(BIN) /home/bbs/bin
clean:
        rm -f $(BIN)
```
========       

由 Makefile 就可以很容易操作了。

在[BBSSRC]/local_utl/dl/下

```
make all
make install 
make clean
```
然后到`/home/bbs/bin`下执行`./anall`就可以了.


--
※ 来源:．天大求实 BBS bbs.tju.edu.cn．[FROM: bbs.tju.edu.cn]