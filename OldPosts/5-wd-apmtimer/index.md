# 更改 WD 硬盘休眠时间

创建于 2021/07/20；编辑于 2024/04/15

---

在使用西数机械硬盘作为移动硬盘使用时，硬盘经常会在很短时间内进入休眠状态，导致使用过程中经常需要等待硬盘启动，使用体验较差。

本文尝试修改西数硬盘的休眠时间，来解决此问题。

## Crystal Disk Info

下载并启动 Crystal Disk Info 软件——功能——高级设置——AAM/APM 控制，点击「高级电源管理（APM）」下面的禁用即可。

但此种方法每次开机都需要重复操作，有没有什么办法可以一劳永逸呢？

## APMtimer

需要一个 Linux 系统，Linux 的基本安装和使用在此不赘述，可以自行查询搜索引擎或者 ChatGPT.

使用 [此软件](https://apmtimer.sourceforge.net/) 即可解决，这是一个 Linux 下的软件。

关于软件的下载、编译和使用方法，重点查阅上述网站中「Quick links」、「Compilation / Installation」和「Syntax」部分。

关于 Linux 的安装和使用，请自行查阅搜索引擎或者 ChatGPT，在此不再赘述。

---
参考

https://www.bilibili.com/read/cv10232535  
http://lxf.me/?p=428
