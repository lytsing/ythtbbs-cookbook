# 性能优化

BBS 的系统架构决定了它只能运行在单机上。YTHT 技术开发组还是做了不少优化的工作。

## Telnet 性能优化

阅读 `doc/INSTALL.chinese`文件，里面有几行代码：

```
[root]# mount none /home/bbs/bbstmpfs -t tmpfs -o size=128m
[bbs]$  cd /home/bbs
[bbs]$  ln -s /home/bbs/bbstmpfs/tmp tmpfast
[bbs]$  ln -s /home/bbs/bbstmpfs/dynamic dynamic
```

tmpfs是一种虚拟内存文件系统，是基于内存的文件系统，Linux 中可以把一些程序的临时文件放置在tmpfs中，利用tmpfs比硬盘速度快的特点提升系统性能。07年我在某公司工作的时候，发现给腾讯 QQ 空间做加速时，也是这么干的，把几个G的静态资源小文件加载到内存中，以空间换时间。


## CGI 性能优化

www 服务使用了 Apache的两个模块：

- Apache fastrw 模块
- Apache fastcgi 模块，提高 cgi 的执行效率。

为了优化性能以及定制化开发，Apache 最后都需要手动修改编译代码，具体见 `software/apachelimit.patch` 与 `software/fastcgi_bufsize.patch`

在当时看来是不错的选择。目前 Lighttpd 与 Nginx 拥有比 Apache 有更好的性能，特别是针对文件下载。
