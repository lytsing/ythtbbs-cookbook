# 分区导读页面的设计

```
发信人: ylsdd.bbs@ytht.net (六月麦田), 信区: BBSDev 
标  题: 一塌糊涂代码中分区导读页面的设计 
发信站: YTHT (Fri Feb 28 17:25:36 2003), 转信 
转信站: NJU!news.zixia.net!news.happynet.org!YTHT 
  
  
(请先阅读 <<一塌糊涂代码最近对版面分区方式的更改简介>>) 
  
糊涂导读最初实现时是采用全站唯一导读方式：bbssec生成全站导读， 
bbsboa显示各个分区的版面列表。但是在站点越来越复杂的情形下， 
这样的唯一导读方式显得越来越不方便： 
    1、bbssec显示的内容越来越多，使得页面体积变大，而用户查看 
       起来也感觉杂乱。 
    2、有些分区很庞大，但在bbssec中只能显示寥寥几条，不够用。 
    3、bbsboa开列版面数目过多，相当多的版面因此得不到注意，更 
       倾向萧条。 
  
为改变这种情况，一塌糊涂代码中实现了树状目录结构，而先前的单一 
WWW导读的方案也相应做了修改，从而可以为每个区实现定制的导读。 
下面就新的导读的细节问题做一介绍。 
  
1、现在导读全部由bbsboa调用来展示，废气bbssec。bbsboa工作流程是： 
   1> bbsboa调用依照参数secstr从sectree（分区树）中选择相应的分区， 
   2> 然后尝试读取“分区导读设置文件”，"wwwtmp/secpage.sec"+secstr。 
      比如对根分区，这个就是 "wwwtmp/secpage.sec", 如果是原创区, 
      secstr="Y"，这个文件就是 "wwwtmp/secpage.secY"。 
   3> 如果成功打开分区导读设置文件，就按照分区导读设置文件中的指令来显示 
      分区导读 
   4> 否则显示默认的分区导读。 
（bbsboa分代码尚未清理干净，可以看到一些先前方案的残迹，不过过些日 
子会清理掉） 
  
2、分区导读设置文件由导读指令行和html代码组成。这些html代码仅包含 
   <body>和</body>之间的内容（不包含<body>和</body>）。指令行被解释 
   执行。html代码都被原样输出。 
       指令行由首字母"#"来标记，后面紧接指令，如果指令之后有参数， 
   则参数和指令中间相隔仅一个空格字符' '。目前实现有如下几种指令： 
       #showsechead       //显示到其它大区的连接 
       #showfile filename //插入文件到输出页面 
       #showsecnav        //显示该区的精彩文章，未完全实现 
       #showstarline 焦点·热点    //将"焦点·热点"前面配一个星号显示 
       #showhotboard      //显示该区热门讨论区，未完全实现 
       #showsecintro      //显示该分区各个子分区的导读，这个导读由 
                          //local_utl/printSecLastMark程序生成，存储 
                          //到文件"wwwtmp/lastmark.sec"+secstr中。 
                          //参见下一段的介绍。 
       #showblist         //显示该分区中不在子分区的那些版面。 
                          //比如大区的管理版面，不适宜放到任何子分区中， 
    举一个例子，糊涂原创区的分区字符串为secstr="Y"，目前的分区导读设置 
    文件wwwtmp/secpage.secY的内容为(每行前面那些空格在实际文件中是没有的）： 
        <center> 
        #showsechead 
        <!-- begin announce --> 
        #showfile 0Announce/groups/GROUP_0/boardname/announce 
        <!-- end announce --> 
        <FONT class=f3><b>--== 讨论区导读 ==--</b></FONT> 
        #showsecintro 
        <hr> 
        #showblist 
3、printSecLastMark程序如何生成讨论区导读 
    1> searchLastMark程序定时将各个版面中的精华文章提取出来，存到 
       wwwtmp/lastmark/中去。 
    2> printSecLastMark程序读取所有版面的精华文章列表。然后遍历 
       sectree中的各个分区，如果分区sec的introstr不为空，就按照 
       sec->introstr的次序依次将sec的各个子分区sec->subsec[i]的 
       导读输出到"wwwtmp/lastmark.sec"+sec->basestr中去。 
通常应该将searchLastMark; printSecLastMark放到crontab中去。 
    3> sec->introstr由libythtbbs/seclist.txt中以*开头的那些行决定， 
       seclist.txt应该依照各站的具体情形来设置。 
           每次修改seclist.txt之后，应该在libythbbs中make install 
       然后在local_utl中重新编译printSecLastMark，安装到bin中去， 
       这样才能使之生效。 
  
  
-- 
※ 修改:．ylsdd 于 Feb 28 17:23:15 修改本文．[FROM: 162.105.31.222] 
※ 来源:．一塌糊涂 BBS http://ytht.net[FROM: 162.105.31.222] 
-- 
```
