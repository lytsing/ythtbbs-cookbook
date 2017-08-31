# CentOS 6 安装YTHT代码指南

> 注意：在CentOS 6.5、6.6版本测试通过，只能安装在 i386/i686 32bit机器上，x86-64无法安装。

## 安装编译工具

    yum install gcc automake gd-devel
    yum install git telnet gdb httpd

### vim 编辑器设置

代码许多文件是 gbk编码的，vim　默认的编码方式是UTF-8,防止打开文件出现乱码，编辑　`~/.vimrc`，增加:

    " for chinese character
    let &termencoding=&encoding
    set fileencodings=utf-8,gbk,ucs-bom,cp936

### Apache 配置
编辑 `/etc/httpd/conf/httpd.conf`，末尾加入:

```
NameVirtualHost *
<VirtualHost *>
RewriteEngine on
RewriteRule ^/Ytht.Net(.*)/bbschat(.*) /cgi-bin/www/bbschat [PT]
RewriteRule ^/Ytht.Net(.*)$ /cgi-bin/www [PT]
RewriteRule  ^/$        /cgi-bin/www [PT]
</VirtualHost>
```

其他地方用户与路径也要相应修改：

```
User bbs
Group bbs

DocumentRoot "/home/bbs/httpd/html/"

<Directory "/home/bbs/httpd/html">
...
</Directory>

ScriptAlias /cgi-bin/ "/home/bbs/httpd/html/cgi-bin/"

```
