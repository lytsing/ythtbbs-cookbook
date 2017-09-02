# BBS程序设计3--常用函数(上)

```
发信人: loveyou (独梦人), 信区: Das_Room
标  题: BBS程序设计3--常用函数(上)
发信站: 锦城驿站 (Tue Apr 25 20:32:51 2000), 转信

当您来到src目录里,您的头一定大了一倍.呵呵,别怕,这里有很多不用的东东.
首先是*.o文件,它是编译BBS时产生的输入文件(* 注意,编译BBS时并不是对于
每一个文件都产生一个可执行文件 *) 如FB7.0是产生bbsd的可执行文件,它是
BBS驱动程式.这里也不多说了.反正*.o的东东您不要管.

不说没有用的了,就说有用的吧,告诉你*.c的文件都是BBS的源程序.它们是有用
的,这么说直接了吧.:PP

才开始学,您只要看*.C文件就行了.

下面,我开始一一讲.(* 工程确实很大,唉,慢慢来吧,我尽量用易懂的方法讲 *)

用户一开始登陆,首先系统调用的是bbsd.c程序,里面一般您不要改,除非您对
操作系统很熟,对BBS程序很熟.它能被用户看到的只有进站画面前面的一行:
     当前系统负荷 .....(我记不清了:PP)
     当前系统负荷 .....(我记不清了:PP)

一一讲实在太多,我拿常用到的讲讲吧,那些不常用的,以后我有时间再说.

======================================================================
说这些之前,我先告诉大家一些BBS编程中常常用到的函数.
======================================================================
currentuser
    这是一个全局的常量.它是一个userec结构.你在编程中可以随时的使用,而不
需要定义.它个各种属性您可以在include目录的struct.h文件里查到.我在这里给
大家贴出来讲一下:
struct userec {
        char            userid[IDLEN+2];   //用户代号
//        char            fill[30];
        time_t          firstlogin;        //用户第一次上站的时间
        char            lasthost[16];      //用户最后一次上站的地址
        unsigned int    numlogins;         //上次次数
        unsigned int    numposts;          //发表文章数
        char            flags[2];          //好象定义标识的(我也不太清楚)
        char            passwd[PASSLEN];   //用户的密码
        char            username[NAMELEN]; //用户的妮称
        char            ident[NAMELEN];    //在main.c中设置的.不用管它.
        char            termtype[STRLEN];  //用户的终端类型
        char            termtype[STRLEN];  //用户的终端类型
        unsigned        userlevel;         //用户的权限
        time_t          lastlogin;         //用户最后一次上站时间
        time_t          stay;              //用户在站的总共停留的时间
        char            realname[NAMELEN]; //真实姓名
        char            address[STRLEN];   //真实住址
        char            email[STRLEN];     //真实E-mail
        int             signature;         //目前使用的签名档
        unsigned int    userdefine;        //用户的参数设置
        time_t          notedate;          //用户上次看留言板的时间
        int             noteline;          //看过留言板的行数
        int             notemode;          //用户看留言板的模式(全看,只看没
                                             看过的,都不看)
//        int           unuse1;/* no use*/ //为以后填加更多的属性而设置的,没用
//        int           unuse3;/* no use*/
};

     这些属性的用法前面我已经说过,我再说一次:

           如果你想得到当前用户的上站次数就是currentuser.numlogins
           它的返回值就是.
           你不用全都记住,用到时再来查,慢慢的你就会记住了.
===========================================================================
===========================================================================
HAS_PERM()
       这个函数也是常用的,是判断当前用户是否具有某个权限.
       如当某用户在文章前按下d时,系统要判断这个用户是否具有板主的
       权限,如果没有,就马上返回:
           if ( !HAS_PERM(PERM_BOARDER) )   return;
       PERM_BOARDER是在权限设置里设置好了的，你可以到include目录下的
       permissions.h文件里查找。下面我例出来讲解一下：
        "基本权力",             /* PERM_BASIC */
        "进入聊天室",           /* PERM_CHAT */
        "呼叫他人聊天",         /* PERM_PAGE */
        "发表文章",             /* PERM_POST */
        "使用者资料正确",       /* PERM_LOGINOK */
        "禁止发表文章",         /* PERM_DENYPOST */
        "隐身术",               /* PERM_CLOAK */
        "看穿隐身术",           /* PERM_SEECLOAK */
        "帐号永久保留",         /* PERM_XEMPT */
        "编辑进站画面",         /* PERM_WELCOME */
        "板主",                 /* PERM_BOARDS */
        "帐号管理员",           /* PERM_ACCOUNTS */
        "本站智囊团",           /* PERM_CHATCLOAK */
        "投票管理员",           /* PERM_OVOTE */
        "系统维护管理员",       /* PERM_SYSOP */
        "系统维护管理员",       /* PERM_SYSOP */
        "Read/Post 限制",       /* PERM_POSTMASK */
        "精华区总管",           /* PERM_ANNOUNCE*/
        "讨论区总管",           /* PERM_OBOARDS*/
        "活动看版总管",         /* PERM_ACBOARD*/
        "不能 ZAP(讨论区专用)", /* PERM_NOZAP*/
        "强制呼叫",             /* PERM_FORCEPAGE*/
        "延长发呆时间",         /* PERM_EXT_IDLE*/
        "特殊权限 1",           /* PERM_SPECIAL1*/
        .....
 以下还有几个特殊权限是没用的。
=====================================================================
DEFINE()
      这个函数是判断用户个人参数里是否设置为YES
      如在发信息的程序里要判断这个用户是否收到信息时发出声音：
           if ( DEFINE(DEF_SOUNDMSG) )   beep(1);

      这些参数在permissions.h里定义了，我例出如下：

        "呼叫器关闭时可让好友呼叫",     /* DEF_FRIENDCALL */
        "接受所有人的讯息",             /* DEF_ALLMSG */
        "接受好友的讯息",               /* DEF_FRIENDMSG */
        "收到讯息发出声音",             /* DEF_SOUNDMSG */
        "收到讯息发出声音",             /* DEF_SOUNDMSG */
        "使用彩色",                     /* DEF_COLOR */
        "显示活动看版",                 /* DEF_ACBOARD */
        "显示选单的讯息栏",             /* DEF_ENDLINE */
        "编辑时显示状态栏",             /* DEF_EDITMSG */
        "讯息栏采用一般/精简模式",      /* DEF_NOTMSGFRIEND */
        "选单采用一般/精简模式",        /* DEF_NORMALSCR */
        "分类讨论区以 New 显示",        /* DEF_NEWPOST */
        "阅读文章是否使用绕卷选择",     /* DEF_CIRCLE */
        "阅读文章游标停于第一篇未读",   /* DEF_FIRSTNEW */
        "进站时显示好友名单",           /* DEF_LOGFRIEND */
        "进站时显示备忘录",             /* DEF_INNOTE */
        "离站时显示备忘录",             /* DEF_OUTNOTE */
        "离站时询问寄回所有讯息",       /* DEF_MAILMSG */
        "使用自己的离站画面",           /* DEF_LOGOUT */
        "我是这个组织的成员",           /* DEF_SEEWELC1 */
        "好友上站通知",                 /* DEF_LOGINFROM */
        "观看留言版",                   /* DEF_NOTEPAD*/
        "不要送出上站通知给好友",       /* DEF_NOLOGINSEND */
        "主题式看版",                   /* DEF_THESIS */
        "收到讯息等候回应或清除",       /* DEF_MSGGETKEY */
        "汉字整字删除",                 /* DEF_DELDBLCHAR */
        "使用GB码阅读"                  /* DEF_USEGB */
        "使用GB码阅读"                  /* DEF_USEGB */
====================================================================
move(x,y)
     将当前光标移到屏幕(x,y)点处。
====================================================================
clear() 与 clrtoeol()
     两上函数都为清屏。
     是有区别的，clear()是指清除当前屏幕所有，也就是全清。
     clrtoeol()是清除当前行，这个函数你只要记住一般常与move()联用。
     如你想在用户屏幕第一行第一例显示一行信息：
          move(1,1);
          clrtoeol();
          prints("看到这行了嘛？");
     这个clrtoeol()函数的目的就是把用户的这一行以前的字符清除，然后
     再把"看到这..."这串字符显示上去.

     所以，当你要清屏时就用clear(),当你要清除当前行时就用clrtoeol()
====================================================================
prints()
     在当前光标处显示某字符串。
     如： prints("HI，你好！");
     也可以带变量：  prints("hi %s,welcome!",currentuser.userid);
     假如当前用户是我，则对我显示： hi loveyou,welcome!
     假如当前用户是我，则对我显示： hi loveyou,welcome!
     根据这个，您可以灵活运用之！
===================================================================
printf()
     把一些字符串保存到某字符串变量里。
     如要把当前用户的BBSE-mail保存到usermail字符串内：
        char usermail[30];
        printf(&usermail,"%s.bbs@%s",currentuser.userid,MY_BBS_DOMAIN);
     则如果当前用户是我，那usermail字符串变量里保存的是：
           loveyou.bbs@bbs.swjtu.edu.cn
     那个MY_BBS_DOMAIN是在include目录下config.h文件内定义的全局常量，还
     有很多有用的，你可以去看看。
=====================================================================

--
※ 修改:．loveyou 于 Apr 26 14:05:29 修改本文．[FROM: 192.168.4.90]
※ 来源:．锦城驿站 bbs.swjtu.edu.cn．[FROM: 192.168.5.117]
```