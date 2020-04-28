[//]: # "Ubuntu 增加 swap 交换空间"

> 本文首发于 https://blog.kangxuanpeng.com/post/ubuntu-add-swap-size

## 背景

在 `apt install` 的时候遇到了错误 FATAL -> Failed to fork.  
```bash
[root@Ubuntu:~]# apt install xxx
Reading package lists... Done
Building dependency tree       
Reading state information... Done
...
FATAL -> Failed to fork.
```

经过排查发现是交换空间不足，所以本文是在 Ubuntu 环境下增加 swap 交换空间。

## 解决步骤

### 查看当前系统 swap 大小

如下所示，当前的系统交换空间已用完：  
```bash
[root@Ubuntu:~]# free -m
              total        used        free      shared  buff/cache   available
Mem:            481         339           9          15         131         114
Swap:             0           0           0
[root@Ubuntu:~]# df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            210M     0  210M   0% /dev
tmpfs            49M  656K   48M   2% /run
/dev/vda1       9.8G  3.1G  6.3G  34% /
tmpfs           241M     0  241M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           241M     0  241M   0% /sys/fs/cgroup
tmpfs            49M     0   49M   0% /run/user/1000
```

### 指定 swap 文件

/* 必须用 root 账号

```bash
[root@Ubuntu:~]# mkdir /swap
[root@Ubuntu:~]# cd /swap/
[root@Ubuntu:swap]# ls
[root@Ubuntu:swap]# dd if=/dev/zero of=swapfile bs=100M count=50
50+0 records in
50+0 records out
5242880000 bytes (5.2 GB, 4.9 GiB) copied, 43.7988 s, 120 MB/s
```

> 命令解释：
> 1 if=文件名：输入文件名，缺省为标准输入。即指定源文件。< if=input file >
2 of=文件名：输出文件名，缺省为标准输出。即指定目的文件。< of=output file >
3 ibs=bytes：一次读入bytes个字节，即指定一个块大小为bytes个字节。
obs=bytes：一次输出bytes个字节，即指定一个块大小为bytes个字节。
bs=bytes：同时设置读入/输出的块大小为bytes个字节。
4 cbs=bytes：一次转换bytes个字节，即指定转换缓冲区大小。
5 skip=blocks：从输入文件开头跳过blocks个块后再开始复制。
6 seek=blocks：从输出文件开头跳过blocks个块后再开始复制。
注意：通常只用当输出文件是磁盘或磁带时才有效，即备份到磁盘或磁带时才有效。
7 count=blocks：仅拷贝blocks个块，块大小等于ibs指定的字节数。

### 转换 swap 文件

```bash
[root@Ubuntu:swap]# mkswap -f swapfile
mkswap: swapfile: insecure permissions 0644, 0600 suggested.
Setting up swapspace version 1, size = 4.9 GiB (5242875904 bytes)
no label, UUID=18adba41-e214-48d6-a8d7-eb06d7e7137c
[root@Ubuntu:swap]# chmod 0600 /swap/swapfile 
[root@Ubuntu:swap]# mkswap -f swapfile
mkswap: swapfile: warning: wiping old swap signature.
Setting up swapspace version 1, size = 4.9 GiB (5242875904 bytes)
no label, UUID=148b286b-a2c6-4ae2-beff-d7d3e0403df0
```

### 激活 swap 文件

用 `swapon` 命令来激活，可以看到激活前后的交换空间大小变化  

```bash
[root@Ubuntu:swap]# free -m
              total        used        free      shared  buff/cache   available
Mem:            481         326          17          15         137         127
Swap:             0           0           0
[root@Ubuntu:swap]# swapon /swap/swapfile
[root@Ubuntu:swap]# free -m
              total        used        free      shared  buff/cache   available
Mem:            481         328          15          15         137         125
Swap:          4999           0        4999
```

/* 取消挂载则是 swapoff swap

至此，成功增加了交换空间。又可以愉快地 `apt install` 了。