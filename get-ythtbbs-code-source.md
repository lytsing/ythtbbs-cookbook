# 代码下载

在一塌糊涂BBS [04年公告](http://bbsdev.ytht.net/Ytht.Net/?t=1504155844)页面，有写着可以使用 svn 按以下命令下载一塌糊涂 BBS 源代码：

```
mkdir ythtsrc
cd ythtsrc
svn checkout svn://ytht.net/bbs/trunk bbs
```

目前无法下载，svn 服务已经停止。我个人电脑上保存的一份代码是原来 svn 库里 2008-08-23 版本的

```
deli@ubuntu:~$ svn co svn://ytht.net/bbs/trunk bbs --revision 7676
```

已经放在 Github 上，下载如下：

```
mkdir bbssrc
cd bbssrc
git clone https://github.com/lytsing/ytht.git
```

也可以下载 西安交大 BBS 的源代码，他们的代码也是托管在 github 上：

```
git clone https://github.com/bmybbs/bmybbs.git
```
顺便提一下，交大的维护组整理了不少的参考文档，在`doc`目录下可以查找到。