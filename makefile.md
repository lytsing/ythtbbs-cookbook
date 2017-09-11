# 编译脚本

项目使用 automake 来组织代码，我们关注一下几个文件：

- makedist.sh 调用 aclocal、autoconf
- configure.ac
- Makefile.am

```
SUBDIRS = . ythtlib libythtbbs src local_utl nju09 include
.PHONY: install-exec-local
install-exec-local:
	./Installbbs.sh
update:
	$(MAKE) -C src install
	$(MAKE) -C local_utl install
	$(MAKE) -C nju09 install
	$(MAKE) -C smth_sshbbsd update
EXTRA_DIST = doc site bbshome

dist-hook:
	for dirname in $(EXTRA_DIST); do \
	  rm -rf `find $(distdir)/$$dirname -name .svn`; \
	done
```

- Makerule.base

    全局性的Makefile,下面的各级目录中 Makefile 会 include 这个文件。

## 在保留数据的情况下升级BBS

一般功能改变直接在相应目录 `make && make installl`就行了，别在总的目录`make install`，子目录如下：

- local_utl `make install`
- nju09 `make isntall`
- src `make installbbs`

或直接在 $BBSSRC 下面 `make update `，而不要 `make install`

如果移植BBS，保留数据就是把  `0Announce/` `wwwtmp/` `boards/` `vote/` `home/` `mail/` `.PASSWDS`和`.BOARDS`,`.BOARDAUX`   备份迁移。

