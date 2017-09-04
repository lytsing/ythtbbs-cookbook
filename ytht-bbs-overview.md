# YTHT BBS 代码解读

本章节讲述 BBS 代码结构、系统架构设计。

说实话，ytht 的代码写的并不是很美观，一是历史代码的包袱，二是疲于做系统优化以及添加新功能，文档寥寥无几，一度遭人吐槽。一个零 Linux + C 基础的大学生，起码需要一年的时间去学习摸索，才能上手。

大部分的文章来自于一些 BBS 的 bbsdev 精华区，现在也不容易搜索到。水木社区、南京大学小百合、上海交大饮水思源等还对外开放，可以到这些 BBS 寻找。

## 代码阅读工具

- Windows 推荐使用 Source Insight
- Linux 推荐 vim 以及 [Square 团队开源的 Vim 配置文件](https://github.com/square/maximum-awesome), Square的配置只能在 macOS 上安装，有人迁移到了 [Ubuntu](https://github.com/justaparth/maximum-awesome-linux) 以及 [Fedora](https://github.com/iboxpay/maximum-awesome-linux)
