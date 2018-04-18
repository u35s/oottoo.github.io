---
layout: post
title:  内存管理
---

由redis内存管理想到golang内存管理(tcmalloc)

由mmap映射进程内存到boltdb中mmap映射数据文件

被映射的内存的边界(最后一个有效地址)常被称为(program break)系统中断点或者当前中断点。
在很多 UNIX® 系统中,为了指出当前系统中断点,必须使用sbrk(0)函数。
sbrk根据参数中给出的字节数移动当前系统中断点,然后返回新的系统中断点。
使用参数0只是返回当前中断点。

32位linux进程地址空间

![linux progress](/assets/img/20180418-progress-memory.jpg){:style="width:800px"}

64位linux进程地址空间

pmap详解


ps中的VSZ和RSS

RSS(resident set size),一个任务使用的非交换物理内存(包括共享库,堆栈内存),单位kb

VSZ(virtual memory size),虚拟内存(包括共享库,堆栈内存,交换空间),单位kb

Links:
[内存管理内幕](http://www.ibm.com/developerworks/cn/linux/l-memory/)
