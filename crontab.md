#  定时程序

安装完发现近日精彩话题、十大热门话题都是空，这些页面显示都是通过定时任务去抓取数据的。

在 $BBS_HOME 目录下新建 `crontab.bbs`，内容如下：

```
#to use this, your bbs user's home dir must be your bbs_home dir
TERM=ansi
PATH=/bin:/usr/bin:/usr/local/bin:/home/bbs/bin
*/5 * * * * upnew >> reclog/uptime.log
56 * * * * averun reclog/uptime.log;/bin/rm  reclog/uptime.log
5 0 * * * /bin/cp -af 0Announce/bbslist/countusr 0Announce/bbslist/countusr.last
5 0 * * * /bin/cp -af 0Announce/bbslist/board2 0Announce/bbslist/board2.last
5 0 * * * /bin/cp -af 0Announce/bbslist/newacct.today 0Announce/bbslist/newacct.last
5 0 * * * /bin/cp -af 0Announce/bbslist/today 0Announce/bbslist/yesterday;/bin/rm reclog/ave.src
5 0 * * * cp -af /home/bbs/etc/posts/day /home/bbs/etc/posts/day.last
10 * * * * newtop10 -a &> /dev/null
10 * * * * newsday.sh
*/20 * * * * save_brc &>/dev/null
30 1 * * * newboards
50 * * * * /home/bbs/bin/nbstat s 7
58 * * * * nbstat b 1
59 * * * * nbstat u 1
7 * * * * jingcai
1,7,13,19,25,31,37,43,49,55 * * * * bbsstatlog;/home/bbs/bin/bbsstatproclog > /home/bbs/reclog/bbsstatproc.log;/usr/bin/gnuplot < /home/bbs/etc/bbsstatlog.plt 
2,32 * * * * nice find bbstmpfs/userattach/* -type d -amin +30 -exec rm -r '{}' \; &>/dev/null
4,34 * * * * nice find bbstmpfs/zmodem/* -type d -amin +30 -exec rm -r '{}' \; &>/dev/null
*/30 * * * * nice find bbstmpfs/bbsattach/* -type f -amin +30 -exec rm '{}' \; &>/dev/null
20 1 * * *  nice -10 bbsheavywork.sh &> syslog/work_log
1 0 * * * daily.sh &> /dev/null
#2 2,8,14,20 * * * autoBanBoards 2 &> syslog/autoBanboards
#5 0,1,3,4,5,6,7,9,10,11,12,13,15,16,17,18,19,21,22,23 * * *  autoBanBoards 1 &> syslog/autoBanboards
13,43 * * * * nice searchLastMark; printSecLastMark; printSecLastUpdate
13,35,58 * * * * nice nav &>/dev/null
0 1 * * * newboards
0 3 * * * MakeIndex3.sh
2 0 * * * userbak.sh
0 8 * * * delvote.sh &> /dev/null
*/5 * * * * if [ etc/postb.pm -nt /tmp/postb.pm ]; then cp etc/postb.pm /tmp/postb.n; chmod 444 /tmp/postb.n;mv /tmp/postb.n /tmp/postb.pm; fi
17 1 * * * autoexecutioner &> /dev/null
13 0 * * * autoundeny &> /dev/null
5,25,45 * * * * printSecLastDigest
*/10 * * * * basketball
#29 1 * * * auto_deluser
#30 1 * * * auto_kickout
0 2 * * * showwhoperm > ~/0Announce/specialperm
#0 8,20 * * * mail_jingcai y@wapku.cn
30 9,13,18,22 * * * statIP 0
15 0 * * * statIP 1; gnuplot < /home/bbs/etc/statIP.plt
58 23 * * * /home/bbs/bin/bbsstatproc2 > /home/bbs/ftphome/root/boards/bbslists/stat.txt
*/10 11,12,13,14,19,20,21,22 * * *  poster &> /dev/null
30 9,11,13,15,17,19,21,23 * * * auto_active &> /dev/null
#59 23 * * * stat_ip &> /dev/null
#04 0 * * * stat_post &> /dev/null
##cn转信
#*/2 * * * * /home/bbs/inndlog/bbsnnrp news.cn99.com /home/bbs/inndlog/cnnews.active > /dev/null 2>&1
#*/1 * * * * /home/bbs/inndlog/bbslink ~bbs >~bbs/tmp/portnum
## bellow by deli for moneycenter 2007.2.26
#0 1 * * * cp /home/bbs/bbstmpfs/dynamic/*.png /home/bbs/ftphome/root/boards/bbslists
#10 5 * * *  /home/bbs/bin/markday > /dev/null 2>&1
#15 5 * * *  /home/bbs/bin/calcfun > /dev/null 2>&1
#20 5 * * *  /home/bbs/bin/calcprison > /dev/null 2>&1
#25 5 * * *  /home/bbs/bin/calctop > /dev/null 2>&1
#30 5 * * *  /home/bbs/bin/richmantop > /dev/null 2>&1

```

然后运行：

```
crontab crontab.bbs
```
以上命令大部分来自于 local_utl 目录下编译出来的程序，具体说明见说明文档`doc/local_utl.readme`。
