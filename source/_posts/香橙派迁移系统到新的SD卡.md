---
title: 香橙派迁移系统到新的SD卡
tags:
  - 香橙派
categories:
  - 教程
top_img: '#FED71A'
abbrlink: 7a51f279
date: 2023-02-26 17:20:22
---

# 前言

刚开始用香橙派的时候，用的是一个 16G 的 SD 卡，但随着系统上装的软件越来越多，16G 的空间明显不够用了，于是我打算扩容一下，将 16G 的 SD 卡更换为 64G。但如果换一张大的 SD 卡，重新装系统，装软件、配置环境这些就比较麻烦了，因此考虑将系统迁移，以下方法适用于香橙派以及树莓派。

# 迁移系统

> 准备一张新的 SD 卡，一个 USB 读卡器。

空间大于 64G 的 SD 卡一般默认是`exFAT`格式，而香橙派不识别`exFAT`格式，处理方法将 SD 卡格式化为`FAT32`格式。

Windows 系统格式化大于 32G 的 U 盘或者内存卡，只能选择`NTFS`、`exFAT`，无法直接格式化为`FAT32`格式。这里使用`DiskGenius` 来格式化 64G 的 SD 卡，具体方法今天先不介绍。

![](https://media.canheting.cn/img/202302261728271.png)

![](https://media.canheting.cn/img/202302261728022.png)

启动香橙派，利用读卡器把新 SD 卡插入树莓派 USB 口。

1. 在命令行输入`df -h`，查看 SD 卡是否识别。

```sh
root@orangepi5:~# df -h
Filesystem      Size  Used Avail Use% Mounted on
tmpfs           769M   18M  752M   3% /run
/dev/mmcblk1p2   15G   12G  2.7G  82% /
tmpfs           3.8G   16K  3.8G   1% /dev/shm
tmpfs           5.0M  4.0K  5.0M   1% /run/lock
tmpfs           3.8G   16K  3.8G   1% /tmp
/dev/mmcblk1p1  256M   95M  162M  37% /boot
/dev/zram1      188M   36M  138M  21% /var/log
tmpfs           769M   72K  769M   1% /run/user/1000
overlay          15G   12G  2.7G  82% /var/lib/docker/overlay2/8460f03e42d14c5691c112d65ab3ce80c4bae86aaec873980fd51d4e5847ddf5/merged
overlaid        769M   72K  769M   1% /run/user/1000/orangepi-chromium
tmpfs           769M   60K  769M   1% /run/user/0
/dev/sda1        59G   96K   59G   1% /media/orangepi/thinkplus
```

2. 卸载新 SD 卡`sudo umount /dev/sda1`

```sh
root@orangepi5:~# sudo umount /dev/sda1
root@orangepi5:~# df -h
Filesystem      Size  Used Avail Use% Mounted on
tmpfs           769M   18M  752M   3% /run
/dev/mmcblk1p2   15G   12G  2.7G  82% /
tmpfs           3.8G   16K  3.8G   1% /dev/shm
tmpfs           5.0M  4.0K  5.0M   1% /run/lock
tmpfs           3.8G   16K  3.8G   1% /tmp
/dev/mmcblk1p1  256M   95M  162M  37% /boot
/dev/zram1      188M   36M  138M  21% /var/log
tmpfs           769M   72K  769M   1% /run/user/1000
overlay          15G   12G  2.7G  82% /var/lib/docker/overlay2/8460f03e42d14c5691c112d65ab3ce80c4bae86aaec873980fd51d4e5847ddf5/merged
overlaid        769M   72K  769M   1% /run/user/1000/orangepi-chromium
tmpfs           769M   60K  769M   1% /run/user/0
```

3. 格式化 SD 卡`sudo mkfs.vfat /dev/sda1 -I`

```sh
root@orangepi5:~# sudo mkfs.vfat /dev/sda1 -I
mkfs.fat 4.2 (2021-01-31)
```

4. 拷贝系统至新 SD 卡`sudo dd if=/dev/mmcblk1 of=/dev/sda bs=4M`

```sh
root@orangepi5:~# sudo dd if=/dev/mmcblk1  of=/dev/sda  bs=4M
3817+1 records in
3817+1 records out
16012804096 bytes (16 GB, 15 GiB) copied, 3144.95 s, 5.1 MB/s
```

**拷贝时间较长，且中间过程无进度提示。**

没关系，教你一招，保证看到进度。新开一个 terminal，输入`sudo watch -n 5 pkill -USR1 ^dd$`，在返回刚才的窗口，是不是 5 秒刷新一次进度。

```sh
root@orangepi5:~# sudo dd if=/dev/mmcblk1  of=/dev/sda  bs=4M
306+0 records in
306+0 records out
1283457024 bytes (1.3 GB, 1.2 GiB) copied, 31.7812 s, 40.4 MB/s
311+0 records in
311+0 records out
1304428544 bytes (1.3 GB, 1.2 GiB) copied, 37.4343 s, 34.8 MB/s
316+0 records in
316+0 records out
1325400064 bytes (1.3 GB, 1.2 GiB) copied, 42.0409 s, 31.5 MB/s
```

# 新系统测试

香橙派关机后，拔出旧的 SD 卡，将新卡插入然后上电，可以正常启动。

但使用 `df -h` 查看依然为 16G，并没有生效，网上查了下，还需要手动修改分区大小，这里用的是 `fdisk` 命令，从 16G 扩展到 64G（实际可用58G）。

```sh
root@orangepi5:~# df -h
Filesystem      Size  Used Avail Use% Mounted on
tmpfs           769M  9.5M  760M   2% /run
/dev/mmcblk1p2   15G   12G  2.7G  82% /
tmpfs           3.8G   16K  3.8G   1% /dev/shm
tmpfs           5.0M  4.0K  5.0M   1% /run/lock
tmpfs           3.8G   16K  3.8G   1% /tmp
/dev/mmcblk1p1  256M   95M  162M  37% /boot
/dev/zram1      188M   42M  132M  25% /var/log
overlay          15G   12G  2.7G  82% /var/lib/docker/overlay2/8460f03e42d14c5691c112d65ab3ce80c4bae86aaec873980fd51d4e5847ddf5/merged
tmpfs           769M   72K  769M   1% /run/user/1000
overlaid        769M   72K  769M   1% /run/user/1000/orangepi-chromium
tmpfs           769M   60K  769M   1% /run/user/0
```

以下是扩容的步骤：

```sh
root@orangepi5:~# sudo fdisk /dev/mmcblk1

Welcome to fdisk (util-linux 2.37.2).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

This disk is currently in use - repartitioning is probably a bad idea.
It's recommended to umount all file systems, and swapoff all swap
partitions on this disk.


Command (m for help): p #查看当前容量

Disk /dev/mmcblk1: 58.25 GiB, 62542315520 bytes, 122152960 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: 5916F58E-4D47-4B12-B663-32607503A1C0

Device          Start      End  Sectors  Size Type
/dev/mmcblk1p1  61440   585727   524288  256M Linux extended boot
/dev/mmcblk1p2 585728 30932991 30347264 14.5G Linux filesystem

Command (m for help): d #先删除分区
Partition number (1,2, default 2): 2 #/dev/mmcblk1p2，分区号是 2

Partition 2 has been deleted.

Command (m for help): n #新建分区，选择主分区就行，p
Partition number (2-128, default 2): 2 #/dev/mmcblk1p2，分区号还是 2
First sector (2048-122152926, default 585728): #起始扇区 参考上面的start，这里默认即可
Last sector, +/-sectors or +/-size{K,M,G,T,P} (585728-122152926, default 122152926): #结尾扇区，这里默认即可

Created a new partition 2 of type 'Linux filesystem' and of size 58 GiB.
Partition #2 contains a ext4 signature.

Do you want to remove the signature? [Y]es/[N]o: y #如果旧的分区有签名信息，移除

The signature will be removed by a write command.

Command (m for help): p #查看新分区信息，可以看到，58G 已经完全分配
Disk /dev/mmcblk1: 58.25 GiB, 62542315520 bytes, 122152960 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: 5916F58E-4D47-4B12-B663-32607503A1C0

Device          Start       End   Sectors  Size Type
/dev/mmcblk1p1  61440    585727    524288  256M Linux extended boot
/dev/mmcblk1p2 585728 122152926 121567199   58G Linux filesystem

Filesystem/RAID signature on partition 2 will be wiped.

Command (m for help): w #写入
The partition table has been altered.
Syncing disks.
```

接下来重启香橙派 `reboot`，重启之后更新文件系统大小：

```sh
root@orangepi5:~# sudo resize2fs /dev/mmcblk1p2
resize2fs 1.46.5 (30-Dec-2021)
Filesystem at /dev/mmcblk1p2 is mounted on /; on-line resizing required
old_desc_blocks = 1, new_desc_blocks = 4
The filesystem on /dev/mmcblk1p2 is now 15195899 (4k) blocks long.

```

查看新分区信息 `df -h`：

```sh
root@orangepi5:~# df -h
Filesystem      Size  Used Avail Use% Mounted on
tmpfs           769M   18M  752M   3% /run
/dev/mmcblk1p2   57G   12G   46G  21% /
tmpfs           3.8G   16K  3.8G   1% /dev/shm
tmpfs           5.0M  4.0K  5.0M   1% /run/lock
tmpfs           3.8G   16K  3.8G   1% /tmp
/dev/mmcblk1p1  256M   95M  162M  37% /boot
/dev/zram1      188M   44M  131M  25% /var/log
tmpfs           769M   60K  769M   1% /run/user/0
overlay          57G   12G   46G  21% /var/lib/docker/overlay2/8460f03e42d14c5691c112d65ab3ce80c4bae86aaec873980fd51d4e5847ddf5/merged
tmpfs           769M   72K  769M   1% /run/user/1000
overlaid        769M   72K  769M   1% /run/user/1000/orangepi-chromium
```

可以看到，扩容已完成。

# 参考链接

1. [树莓派拷贝系统到新 SD 卡](https://blog.csdn.net/weixin_43444901/article/details/110260864)
2. [超简单的树莓派 SD 卡扩容方案，将树莓派 16GB 内存卡克隆到 64GB 内存卡](https://mp.weixin.qq.com/s/h-a7N65vBYRE6drImfHApA)
2. [香橙派 sd 卡扩容](https://blog.csdn.net/Hallo_ween/article/details/107574829)
