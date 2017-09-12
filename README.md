# YTHT BBS 开发指南

BBS 系统由一塌糊涂 BBS 的系统维护人员和其它爱好者改写，这个系统是在 FireBird BBS 3.0 KCN 版本 上修改来的。在改写的过程中，主要参考和使用了下列 BBS 软件：

> * 老农的 FB2000 的部分代码
* 南京大学小百合站的 zhch 的 njuweb 0.9 的全部代码
* 水木清华BBS的部分代码
* im-httpd, Michael Bacarella
* bftpd, Max-Wilhelm Bruker

原来的开发组已经停止维护，有一些分支还在继续着，生活也是一样继续着。

* [天天坛](http://tttan.com/)，由原维护组三驾马车之一的 ylsdd 维护，代码未公开。
* [西安交大兵马俑](http://bbs.xjtu.edu.cn/)，也维护自己的[分支](https://github.com/bmybbs/bmybbs)
* [一路BBS](http://www.yilubbs.com/)，他们的代码在放在 [google code](https://code.google.com/p/ythtbbs/)，主要是从支持 njuweb 改为支持 discuzx。

Telnet BBS，是20年前流行的技术，现在肯定很难找到关于 BBS 开发技术的书籍，具体的细节问题估计很难找到人请教，都是失传的绝学。一塌糊涂 BBS 系统经维护组不断修改，吸取各种 BBS 软件的长处，形成独立的一套 BBS 软件分支，并被其他一些BBS站采用。虽然 BBS 已经落寞，但其中不少问题的技术方案、解决思路，从今天看来仍然闪光与带有借鉴意义。在腾讯或者百度这样大型的互联网公司，还有许多代码是使用 C 写 CGI，而且从这些公司出来创业的员工，技术选型还是主要是这些东西。

如果想做这方面的维护与开发，以下几本书是必须的手头参考书：

* 《UNIX 环境高级编程》
* 《C 程序设计语言》

Linux 版本，首推荐 Ubuntu、CentOS，因为这两个版本是使用最广泛，遇到问题容易搜索解决。作为练习学习，也推荐使用 [Vagrant](https://www.vagrantup.com/) + VirtualBox 做虚拟化管理。

我没有直接给 YTHT 贡献过一行代码，会写代码的时候，站点已经关闭了。大学期间花了不少精力去研究学习开发 BBS 代码，整天穿梭浏览各大 BBS 的 BBSDev 版翻尸体，各种求助。整理旧电脑硬盘，发现积累不少的文档，丢之可惜，总结整理成文章，希望对 YTHT 代码和 BBS 感兴趣的朋友，以及希望用这个项目提高 Linux C/C++ 开发能力的在校学生提供一些帮助。
