# YTHT BBS 开发指南

BBS 系统由一塌糊涂 BBS 的系统维护人员和其它爱好者改写，这个系统是在 FireBird BBS 3.0KCN 版本 上修改来的。在改写的过程中，主要参考和使用了下列BBS软件：

> * 老农的 FB2000 的部分代码
* 南京大学小百合站的 zhch 的 njuweb 0.9 的全部代码
* 水木清华BBS的部分代码
* im-httpd, Michael Bacarella
* bftpd, Max-Wilhelm Bruker

原来的开发组已经停止维护，有一些分支还在继续着，生活也是一样继续着。

* [天天坛](http://tttan.com/)，由原维护组三驾马车之一的 ylsdd　维护，代码未公开。
* [一见如故](https://yjrg.net/)，已经关站。
* [西安交大兵马俑](http://bbs.xjtu.edu.cn/)，也维护自己的[分支](https://github.com/bmybbs/bmybbs)
* [一路BBS](http://www.yilubbs.com/)，他们的代码在放在 [google code](https://code.google.com/p/ythtbbs/)，主要是从支持 njuweb 改为支持 discuzx。


Telnet BBS，是20年前流行的技术，现在肯定很难找到关于 BBS 开发技术的书籍，具体的细节问题估计很难找到人请教，都是失传的绝学。虽然 BBS 已经落寞，一塌糊涂 BBS 系统经维护组不断修改，吸取各种BBS软件的长处，形成独立的一套BBS软件分支，并被其他一些BBS站采用。其中不少问题的技术方案、解决思路，从今天看来，仍然闪光与借鉴意义。

大学期间花了不少精力去学习开发 BBS 代码，整天穿梭浏览各大 BBS 的 BBSDev 版，各种求助。整理旧电脑硬盘，发现积累不少的文档，丢之可惜，总结整理成文章，希望对 YTHT 代码和 BBS 感兴趣的朋友，以及希望用这个项目提高 Linux C/C++ 开发能力的在校学生提供一些帮助。

