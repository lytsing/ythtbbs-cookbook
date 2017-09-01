发信人: wonsgnihsiF (雪江寒钓独), 信区: BBSDEV
标  题: YTHT代码WWW界面修改指南[第一章：导读页面]
发信站: 数学科学学院公共论坛 (Sat Dec 18 06:17:42 2004), 转信
(bbs.math.pku.edu.cn)

在修改导读页面之前，我们不妨先花一点时间了解一下导读页面的结构。YTHT代码的导
读页面由 `$(BBSHOME)/wwwtmp/secpage.sec`生成。在没有这个文件的时候，代码中有一套默
认的生成方式。[1]

如果在指定的位置找不到这个文件，可以直接将secpage.sec.sample复制为这个文件，
如果没有sample，%@@#Rxxff#，那就去cn-bbs专信组的BBSDEV版查找一下吧，应该有很多人
贴过这个文件。

secpage.sec 相当于一个HTML文档中<body> </body>之间的部分。所以完全可以按照写
HTML文档的方法来写secpage.sec 文件。这个文件决定了页面的样式和布局，是修改 WWW界
面中最重要的文件之一。当然secpage.sec 也支持“导读指令行”的概念，这才是首页导读
最重要的部分。常用的导读指令行包括：[2]

    #showsechead                //显示到其它大区的连接
    #showfile filename          //插入文件到输出页面
    #showsecnav                 //显示用户评价的精彩文章
    #showstarline STRING        //将STRING前面配一个星号显示
    #showhotboard               //显示该区热门讨论区
    #showsecintro               //显示该分区各个子分区的导读
    #showblist                  //显示该分区中不在子分区的那些版面。

这些导读指令行必须在行首出现，否则将作为一般字符处理。在YTHT代码的sample中可
以看到，主要使用了以下几个导读指令行：

    #showsechead
    #showfile
    #showsecnav
    #showstarline
    #showhotboard
    #showsecintro

 除了 #showsecintro之外，其余几个都是完全由$(BBSSRC)/nju09/bbsboa.c负责处理的
，而大部分需要的HTML也都是在这个文件中出现的。

 所以我们所需要修改的内容大概可以限制在三个文件之内：

    $(BBSHOME)/wwwtmp/secpage.sec
    $(WWWHOME)/html/bbslg.css
    $(BBSSRC)/nju09/bbsboa.c

 这里需要注意的是，第二个文件是默认的“蓝色理想”的样式表文件，如果你希望保留
这个风格，请修改其他文件或先备份这个文件。这里选择这个文件的原因是，修改这个文件
可以直接在浏览器中看到效果，而不必在源代码中重新make install。当然这样也有一个问
题，一旦重新make install就会造成修改过的 bbslg.css被覆盖，因此重新make install之
前请务必保证这个文件在某个编辑器中被打开，或者先用此文件覆盖

     $(BBSSRC)/nju09/html/bbslg.css

后再进行make install。

上面说过的指令行大部分可以在bbsboa.c中直接找到对应的函数，通过修改这些函数中
嵌入的HTML后重新make install就可以看到效果了。

 在这里有一个建议，就是尽量少使用 table，多用 div进行定位，尽量少使用 style为
每个标签单独定义样式，多用样式表文件定义类，这样便于今后维护和修改。而且将尽量少
的HTML写在 C语言的代码中。当然可以根据实际情况和个人喜好进行权衡。比如本文中所举
的例子，即 MathBBS的首页导读完全没有用到 table，所有的定位都是通过 div实现的，在
IE和 FireFox下呈现良好。当然做到符合 W3C标准恐怕比较困难。

刚才说过#showsecintro 是个比较特殊的指令行，因为它牵涉到多个文件。这个指令行
的作用是将各个区中比较好的文章集中显示，而显示的布局是由

    $(BBSSRC)/site/seclist.txt

这个文件决定的。在ylsdd@YTHT的《一塌糊涂代码最近对版面分区方式的更改简介》可以找
到关于 seclist.txt的内容。

 回到`#showsecintro` 这个指令行。它牵涉到的文件包括：

    $(BBSSRC)/nju09/html/function.js
    $(BBSSRC)/local_utl/printSecLastMark.c

 其中printSecLastMark.c编译安装后在$(BBSHOME)/bin下面生成同名的程序，运行该程
序将搜索各版面被 m的文章，写入$(BBSHOME)/wwwtmp/lastmark.js.sec 中。这个文件在调
用#showsecintro 的是时候作为一段javascript脚本被bbsboa自动写入该指令行所在的位置
。

   在 printSecLastMark.c中，涉及到HTML的只是各区之间的排列关系，可以在这个文件中
修改两列之间的间距，列宽等等，如果采用 div定位，则可以更灵活一些。在 MathBBS修改
的这个文件中，涉及到HTML的部分被改为：

```
makeallseclastmark(const struct sectree *sec)
{
        int i, n, nline, separate;
        const struct sectree *subsec;
        char buf[200], tmp[200];
        FILE *fp;
        if (!sec->introstr[0])
                return -1;
        sprintf(tmp, "%s/wwwtmp/lastmark.js.sec%s.new", MY_BBS_HOME,
                sec->basestr);
        fp = fopen(tmp, "w");
        if (!fp)
                return -1;
//      fprintf(fp, "<!--\n"
//              "document.write('<table><tr><td valign=top width=49%%>');\n");
        fprintf(fp, "<!--\n"
                "document.write('<li style=\\\'width: 49%%;\\\'>');\n");

        n = strlen(sec->introstr);
        if (strchr(sec->introstr, '|'))
                separate = strchr(sec->introstr, '|') - sec->introstr;
        else
                separate = n / 2;
        nline = 8;
        if (n <= 4 || sec->basestr[0])
                nline = 13;
        for (i = 0; i < n; i++) {
                if (i == separate)
                        fprintf(fp,
//                              "document.write('</td><td valign=top "
                                "width=49%%>');\n");
                                "document.write('</li><li style=\\\'"
                                "width: 1%%;\\\'>&nbsp;</li>"
                                "<li style=\\\'width: 50%%;\\\>');\n");
                if (sec->introstr[i] == '|')
                        continue;
                sprintf(buf, "%s%c", sec->basestr, sec->introstr[i]);
                subsec = getsectree(buf);
                if (subsec == &sectree)
                        continue;
                if (!strcmp(subsec->basestr, "T")
                    || !strcmp(subsec->basestr, "4"))
                        printseclastmark(fp, subsec, 11);
                else
                        printseclastmark(fp, subsec, nline);
        }
//      fprintf(fp, "document.write('</td></tr></table>');\n//-->\n");
        fprintf(fp, "document.write('</li>');\n//-->\n");
        fclose(fp);
        sprintf(buf, "%s/wwwtmp/lastmark.js.sec%s", MY_BBS_HOME, sec->basestr);
        rename(tmp, buf);
        for (i = 0; i < sec->nsubsec; i++)
                makeallseclastmark(sec->subsec[i]);
        return 0;
}[3]
```

 其中被注释掉的部分是原来用 table控制的布局方式。如果仍然希望使用 table进行布
局控制，可以不修改这个文件。刚才说到 lastmark.js.sec作为一个javascript脚本被嵌入
生成的导读页中。这个脚本中调用了以下几个与样式修改有关的js函数：

    btb('区号', '区名')
    itb('版面编号', '版名', '真实档名', '标题')
    etb()

第一个函数生成每个分区的标题，即默认界面中的“★　知性感性  >>more”这一栏，
第二个函数负责生成每个文章的链接，第三个函数的功能是结束一个分区的显示。这三个函
数的定义都可以在

    $(WWWHOME)/html/function.js 或者
    $(BBSSRC)/nju09/html/funcion.js

中找到。建议修改 $(BBSSRC)中的函数之后重新make install。

====

[1] 感谢 wy@TJUBBS告诉我这一点。

[2] 以下内容参考了ylsdd@YTHT的《一塌糊涂代码中分区导读页面的设计》。

[3] 特别说明一下吧，\\\'在最终的HTML中是一个双引号，\\是 C语言中 \的转义，\'是一
个双引号的转义。所以在生成的js脚本中\\\'被转义为\"，而这个又是js中 "的转义。这样
写起来很麻烦但是貌似也没有更好的办法，如果大量用双引号的话势必造成代码可读性下降
，但不用双引号的HTML也不够规范。

--
※ 来源:．数学科学学院公共论坛 bbs.math.pku.edu.cn．[FROM: 162.105.70.2]