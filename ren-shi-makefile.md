# 编译脚本

项目使用 automake 来组织代码，我们关注一下几个文件：
- makedist.sh

    调用 aclocal、autoconf
- configure.ac
- Makefile.am

- Makerule.base

    全局性的Makefile,下面的各级目录中 Makefile 会 include 这个文件。

