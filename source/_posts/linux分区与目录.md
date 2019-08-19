---
title: linux分区与目录
date: 2019-05-29 12:56:51
tags: [linux]
---

#### 分区和目录

Windows的文件结构是**多个并列的树状结构**，最顶部的是不同的磁盘（分区）。Linux的文件结构是**单个的树状结构**。每次安装系统的时候我们都会进行分区，Linux下磁盘分区和目录的关系如下：

- 任何一个分区都必须挂载到某个目录上
- 目录是逻辑上的区分，分区是物理上的区分
- 磁盘Linux分区都必须挂载到目录树中的某个具体的目录上才能进行读写操作
- 根目录是所有Linux的文件和目录所在的地方，需要挂载上一个磁盘分区

<!--more-->

<br/>

```shell
[root@localhost ~]# df -lh
Filesystem       容量      已用      可用      已用%      挂载点
/dev/sda3        6.4G     4.8G      1.3G     80%        /
/dev/sda1        99M      8.4M      86M      9%         /boot
none             494M     0         494M     0%         /dev/shm
/dev/hdc         105M     105M      0        100%       /media/cdrom
```

①左边的信息代表了磁盘文件系统分区：`/dev/sda1, /dev/sda3`等

②右边的信息分别代表了**容量，已用空间，可用空间，百分比，挂载点**

<br/>



#### 磁盘占满

磁盘占满，会导致nginx无法缓存文件，报`net::ERR_CONTENT_LENGTH_MISMATCH`错误。

<br/>



