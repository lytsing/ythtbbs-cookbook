# 编译脚本

项目使用 automake 来组织代码，我们关注一下几个文件：
makedist.sh

    调用 aclocal、autoconf
configure.ac

Makefile.am

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

