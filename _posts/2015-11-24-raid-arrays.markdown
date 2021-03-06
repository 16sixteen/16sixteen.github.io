---
layout:     post
title:      "自制阵列实验的收获"
subtitle:   "raid0 raid1 raid0+1 raid1+0 raid5"
date:       2015-11-24 15:40:00
categories: data-storage
tags:       数据存储
author:     "SixTeen"
header-img: "img/solvingReport.jpg"
published:   true
---

<a href="#01">raid0</a> <a href="#02">raid1</a> <a href="#03">raid0+1</a> <a href="#04">raid1+0</a> <a href="#05">raid5</a>

----

自制阵列实验的收获，是自己结合实际的一些猜想，不能保证正确性。问题摘自实验报告的问题。

###单盘性能

Q1.1：结合硬盘的构造特点和相关技术对硬盘的性能差异进行对比分析。

在随机读性能上，ssd使用的是闪存颗粒，因此没有机械硬盘的寻道时间，直接从闪存颗粒上读取，在随机读上比机械硬盘快2倍以上。而PCIE接口的ssd相对于普通的sata接口的ssd的速度又要快上几个级别。因为PCIE接口的ssd直接连接在北桥芯片上，极大减少了传输时延，带宽和处理速度都比sata口的要快上数倍，无论是在读还是写，都是碾压级别的。对于随机写性能，虽然ssd没有机械硬盘的寻道时间影响，但是由于ssd硬盘的闪存颗粒是有一定寿命的，所以ssd的写由于要遵循负载均衡的原因会造成读写速度不对称，因此ssd的写速度会低于读速度。对于大文件的处理，全都是连续操作的情况下，机械硬盘已经达到了它单盘理论140m/s左右的理论阈值，sata的ssd在连续大文件读也达到了它的250m/s，而pcie的ssd在大文件连续读达到1500m/s，在大文件写上，sata口的ssd的速度低于机械硬盘。

Q1.3：并发数1与32对硬盘性能有何影响

机械硬盘：在小文件读性能上，并发的32个能够让硬盘进行NCQ，优化磁头寻道顺序，减少了大量的旋转等待的时间，因此小文件读在并发32的时候会比1的时候快非常的多。在大文件上，都是进行机械磁盘最擅长的连续读，并发32和并发1都是读取完1个，再进行1个，因此没有什么影响。在小文件的随机写上面，可以看到并发1和并发32的结果基本是一样的。并发1和并发32都应用了NCQ，并发1在得到一个写请求的时候，还没写入的时候，就索求下一个写请求，也能进行一定的NCQ，因此速度基本相同。但在连续写的时候，NCQ的作用就不大了，因为都是连续的顺序写，但是这个时候频繁的索求就会造成一定的消耗，因此并发1比并发32要慢。而对于大文件，并发1和并发32对于机械硬盘的影响就不大了，因为无论是并发1还是并发32对于机械硬盘都是一个接一个大文件的连续读写操作。

SSD：在小文件读性能上，闪存上的最小单位是128k，因此并发的32个数据能让ssd减少访问块的次数（因为如果有些数据在同一个块里，我们就能减少访问该块的次数），在小文件写上，每次写入修改都是以一个块为单位的，随机单个小文件的写入会让ssd造成碎片化，带来垃圾回收，数据迁移开销，因此并发的32个数据经过硬盘缓存，能够减少这些开销。在pcie接口的ssd的测试数据中我们可以看到这些开销是非常大的。在大文件上，固态磁盘和机械磁盘一样，并发1和并发32效果是一样的，因为也都是一个接一个的大文件读写操作。


RIAD(Redundant Arrays of Independent Disks)，组成RAID的好处是有很高的可靠性，加快IO。


### <a name="01"/>RAID0

RAID0所做的就是把数个磁盘条带化(striping)从而实现并行读写，因此读写速度理论能达到N倍(磁盘个数)。RAID0单个磁盘损坏数据就不可修复了。

![](/img/data/stripe.png)
![](/img/data/raid0.png)

### <a name="01"/>RAID1

RAID1所做的就是对磁盘做镜像(mirroring),从事实现对磁盘损坏时可以进行修复。RAID1的读取速度是原来的2倍，写接近单盘速度，不过2个盘只能使用1个盘的容量使得raid1的价格昂贵。

![](/img/data/raid1.png)

### <a name="03"/>RAID0+1

0+1就是先raid0，再做raid1，理论上能容忍最多2个盘的损坏。raid0+1的缺点是如果raid0部分坏一个，raid1部分坏一个，就无法修复了，因为是按条带进行镜像的，因此镜像raid1部分坏一个就会导致数据无法恢复。

### <a name="04"/>RAID1+0

1+0和0+1类似，不过是先对每个盘做raid1，再做raid0。这样做的好处就是能容忍任意2个盘的损坏，比raid0+1要安全，速度又是一样，这也是0+1变得少见的原因。

### <a name="05"/>RAID5

把奇偶校验平均到每个盘中，防止了raid4中校验盘的集中频繁读改写。

![](/img/data/raid5.png)

原理：异或的互换性

$$ A = B \oplus C \oplus D $$

$$ B = A \oplus C \oplus D $$

$$ C = A \oplus B \oplus D $$

$$ D = A \oplus B \oplus C $$

    FIN 12.9/12.59