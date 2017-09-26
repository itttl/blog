<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2015-04-02
title: 如何使用parted对齐分区以得到最优性能
tags: linux,parted,调优
category: Linux
status: publist
summary: 如何使用parted对齐分区以得到最优性能
-->

![gitblog-logo](./img/logo_64x64.png)

## How to align partitions for best performance using parted
There are two common problems when creating partitions in Linux on big storage arrays. 
The first is easy, and the warning message from fdisk is a bit of a giveaway:
```
WARNING: The size of this disk is 8.0 TB (7970004230144 bytes).
DOS partition table format can not be used on drives for volumes
larger than (2199023255040 bytes) for 512-byte sectors. Use parted(1) and GUID 
partition table format (GPT).
```
The answer: use parted. Don’t have it? Install it!  

The second problem is this warning from parted:  
```
(parted) mklabel gpt
(parted) mkpart primary 0 100%
Warning: The resulting partition is not properly aligned for best performance.
Ignore/Cancel?
```

…and no matter what combination of numbers you use, the message just keeps  
coming back. It’s tempting to ignore it, but don’t.

There are a few posts on the subject, but this one from HP really gets to   
the guts of the problem.

Here’s a quick step-by-step guide to aligning partitions properly. It’s just  
an abstraction of the HP post, but hopefully easier to follow. This will work   
for most arrays (in fact it works for all the arrays that I’ve seen); there   
are more options in HP’s post, but I’ve included the most common configuration here.

- 1.Get the alignment parameters for your array (remember to replace sdb with the   
name of your device as seen by the kernel).
```
# cat /sys/block/sdb/queue/optimal_io_size
1048576
# cat /sys/block/sdb/queue/minimum_io_size
262144
# cat /sys/block/sdb/alignment_offset
0
# cat /sys/block/sdb/queue/physical_block_size
512
```
- 2.Add optimal_io_size to alignment_offset and divide the result by   
physical_block_size. In my case this was (1048576 + 0) / 512 = 2048.
- 3.This number is the sector at which the partition should start. Your new    
parted command should look like
```
mkpart primary 2048s 100%
```
The trailing ‘s’ is important: it tells parted that you’re talking about sectors,  
not bytes or megabytes.   

- 4.If all went well, the partition will have been created with no warnings.   
You can check the alignment thusly (replacing ‘1’ with the partition number if necessary):
```
(parted) align-check optimal 1                                            
1 aligned
```


As I alluded to before, there are cases where this won’t work: if optimal_io_size  
is zero, for example, there are other rules to follow. Of course it would be nice  
if parted could do thisâ€”the values are all available as ioctls, after all”but  
then what would I write about?
