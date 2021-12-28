---
title: Android Device Tree 分区大小计算
date: 2021-02-17 11:37:42
id: '<%= page.date %>'
tags: 
- 随笔
- 原创
- Android
- 技术笔记
---
首先是 `BOARD_FLASH_BLOCK_SIZE`，用 `BOARD_KERNEL_PAGESIZE` 的值乘以 64 即可

其次是众大多分区，我们可以用 `dd of=/dev/null if=partition` 命令来获取大小，但对于大分区来说耗时太长，而且需要root权限

转而一想，为什么不能扒 linux 分区表从而直接获取大小呢
<!-- more -->
这里以 Huawei Watch 2 LTE 为例
```bash
sawshark:/ $ cat /proc/partitions
major minor  #blocks  name

 254        0     262144 zram0
 179        0    3817472 mmcblk0
 179        1        512 mmcblk0p1
 179        2        512 mmcblk0p2
 179        3        512 mmcblk0p3
 179        4        512 mmcblk0p4
 179        5        768 mmcblk0p5
 179        6        768 mmcblk0p6
 179        7       5120 mmcblk0p7
 179        8       5120 mmcblk0p8
 179        9      10240 mmcblk0p9
 179       10       5120 mmcblk0p10
 179       11       4096 mmcblk0p11
 179       12       4096 mmcblk0p12
 179       13          1 mmcblk0p13
 179       14          8 mmcblk0p14
 179       15       1024 mmcblk0p15
 179       16      10240 mmcblk0p16
 179       17        512 mmcblk0p17
 179       18        512 mmcblk0p18
 179       19       5120 mmcblk0p19
 179       20      10240 mmcblk0p20
 179       21         32 mmcblk0p21
 179       22       4096 mmcblk0p22
 179       23       4096 mmcblk0p23
 179       24        256 mmcblk0p24
 179       25        256 mmcblk0p25
 179       26        256 mmcblk0p26
 179       27        256 mmcblk0p27
 179       28      10240 mmcblk0p28
 179       29      32768 mmcblk0p29
 179       30         16 mmcblk0p30
 179       31      32768 mmcblk0p31
 259        0     111568 mmcblk0p32
 259        1    1048576 mmcblk0p33
 259        2      65536 mmcblk0p34
 259        3    2436564 mmcblk0p35
 179       32        512 mmcblk0rpmb
 253        0    1032176 dm-0
```
`#blocks`为 块设备总块数，每个块是 1024 字节，等同于 KB
`name`就是 块设备名称 了，通过列目录可以很方便找到 可读名称 和 块设备名称 的关系
```bash
sawshark:/ $ ls -l /dev/block/bootdevice/by-name
total 0
lrwxrwxrwx 1 root root 21 1970-01-13 18:59 DDR -> /dev/block/mmcblk0p21
lrwxrwxrwx 1 root root 20 1970-01-13 18:59 aboot -> /dev/block/mmcblk0p7
lrwxrwxrwx 1 root root 20 1970-01-13 18:59 abootbak -> /dev/block/mmcblk0p8
lrwxrwxrwx 1 root root 21 1970-01-13 18:59 boot -> /dev/block/mmcblk0p29
lrwxrwxrwx 1 root root 21 1970-01-13 18:59 cache -> /dev/block/mmcblk0p34
lrwxrwxrwx 1 root root 21 1970-01-13 18:59 cmnlib -> /dev/block/mmcblk0p24
lrwxrwxrwx 1 root root 21 1970-01-13 18:59 cmnlibbak -> /dev/block/mmcblk0p25
lrwxrwxrwx 1 root root 21 1970-01-13 18:59 config -> /dev/block/mmcblk0p18
lrwxrwxrwx 1 root root 21 1970-01-13 18:59 devinfo -> /dev/block/mmcblk0p23
lrwxrwxrwx 1 root root 21 1970-01-13 18:59 fsc -> /dev/block/mmcblk0p13
lrwxrwxrwx 1 root root 21 1970-01-13 18:59 fsg -> /dev/block/mmcblk0p22
lrwxrwxrwx 1 root root 21 1970-01-13 18:59 keymaster -> /dev/block/mmcblk0p26
lrwxrwxrwx 1 root root 21 1970-01-13 18:59 keymasterbak -> /dev/block/mmcblk0p27
lrwxrwxrwx 1 root root 21 1970-01-13 18:59 keystore -> /dev/block/mmcblk0p17
lrwxrwxrwx 1 root root 21 1970-01-13 18:59 misc -> /dev/block/mmcblk0p15
lrwxrwxrwx 1 root root 21 1970-01-13 18:59 modem -> /dev/block/mmcblk0p32
lrwxrwxrwx 1 root root 21 1970-01-13 18:59 modemst1 -> /dev/block/mmcblk0p11
lrwxrwxrwx 1 root root 21 1970-01-13 18:59 modemst2 -> /dev/block/mmcblk0p12
lrwxrwxrwx 1 root root 21 1970-01-13 18:59 nfc -> /dev/block/mmcblk0p19
lrwxrwxrwx 1 root root 21 1970-01-13 18:59 nvbin -> /dev/block/mmcblk0p10
lrwxrwxrwx 1 root root 20 1970-01-13 18:59 nvdata -> /dev/block/mmcblk0p9
lrwxrwxrwx 1 root root 21 1970-01-13 18:59 oem -> /dev/block/mmcblk0p28
lrwxrwxrwx 1 root root 21 1970-01-13 18:59 recovery -> /dev/block/mmcblk0p31
lrwxrwxrwx 1 root root 21 1970-01-13 18:59 reserved -> /dev/block/mmcblk0p20
lrwxrwxrwx 1 root root 20 1970-01-13 18:59 rpm -> /dev/block/mmcblk0p3
lrwxrwxrwx 1 root root 20 1970-01-13 18:59 rpmbak -> /dev/block/mmcblk0p4
lrwxrwxrwx 1 root root 20 1970-01-13 18:59 sbl1 -> /dev/block/mmcblk0p1
lrwxrwxrwx 1 root root 20 1970-01-13 18:59 sbl1bak -> /dev/block/mmcblk0p2
lrwxrwxrwx 1 root root 21 1970-01-13 18:59 sec -> /dev/block/mmcblk0p30
lrwxrwxrwx 1 root root 21 1970-01-13 18:59 splash -> /dev/block/mmcblk0p16
lrwxrwxrwx 1 root root 21 1970-01-13 18:59 ssd -> /dev/block/mmcblk0p14
lrwxrwxrwx 1 root root 21 1970-01-13 18:59 system -> /dev/block/mmcblk0p33
lrwxrwxrwx 1 root root 20 1970-01-13 18:59 tz -> /dev/block/mmcblk0p5
lrwxrwxrwx 1 root root 20 1970-01-13 18:59 tzbak -> /dev/block/mmcblk0p6
lrwxrwxrwx 1 root root 21 1970-01-13 18:59 userdata -> /dev/block/mmcblk0p35
```
综合以上，用awk套娃一个自动化脚本

安卓设备默认是不带awk的，所以需要通过电脑套娃

这里以wsl运行环境为例，如果不是wsl自行更改`call_cat`和`call_ls`两个变量即可
```bash
#!/bin/bash
call_cat="adb.exe shell cat" # cat命令
call_ls="adb.exe shell ls" # ls命令
name_base="/dev/block/bootdevice" # by-name的上级目录，一般不用动
if [[ -n $1 ]]; then
  block_name=$(${call_ls} -la ${name_base}/by-name/$1 2>/dev/null | awk -F " -> /dev/block/" '{print $2}') # 将 分区可读名称 转换为 块设备名称
  if [[ -n ${block_name} ]]; then
	${call_cat} /proc/partitions | grep ${block_name} | awk -F " " '{print $3 * 1024}' # 输出单位为 Byte
  else
    echo "Error: partition $1 not found"
  fi
else
  echo "Usage: $0 [partition name]"
fi
```
运行测试
```bash
mlgmxyysd@MlgmXyysd-NUC:~$ ./partitions.sh
Usage: ./partitions.sh [partition name]
mlgmxyysd@MlgmXyysd-NUC:~$ ./partitions.sh non_exist_partition
Error: partition non_exist_partition not found
mlgmxyysd@MlgmXyysd-NUC:~$ ./partitions.sh system
1073741824
```
与上方输出计算结果一致，成功


更新：上面的方式在一些设备中可能会被SELinux挡掉

其实还有一种更简单的方式

重启设备至`fastboot`或`bootloader`模式
```bash
fastboot getvar partition-size:[partition name]
```
例如：
```bash
mlgmxyysd@MlgmXyysd-NUC:~$ fastboot.exe getvar partition-size:system
partition-size:system:   0x40000000
Finished. Total time: 0.116s
```
返回值转换为 DEC 为 1073741824 ，单位为 Byte


感谢：[TH779](https://blog.779.moe/archives/7/)