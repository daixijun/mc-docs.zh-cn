---
title: include 文件
description: include 文件
services: virtual-machines
ms.service: virtual-machines
ms.topic: include
origin.date: 01/04/2021
author: rockboyfor
ms.date: 11/02/2020
ms.testscope: no
ms.testdate: 11/02/2020
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 7eb28da2d98297895aac50c038dc2606c1d940bd
ms.sourcegitcommit: b4fd26098461cb779b973c7592f951aad77351f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97857156"
---
<!--Verified Successfully-->
![突出显示了 r=22.8k 的 f i o 输出屏幕截图。](media/vm-disk-performance/utilization-metrics-example/fio-output.jpg)

Standard_D8s_v3 最多可以达到 28,600 个 IOPS。 让我们使用这些指标，调查正在进行的操作，并确定存储 IO 瓶颈。 在左侧窗格中，选择“指标”：

![左侧窗格中突出显示了“指标”的屏幕截图。](media/vm-disk-performance/utilization-metrics-example/metrics-menu.jpg)

我们先看一下 **已使用的 VM 缓存 IOPS 的百分比** 指标：

![显示“已使用的 VM 缓存 IOPS 的百分比”的屏幕截图。](media/vm-disk-performance/utilization-metrics-example/vm-cached.jpg)

此指标告诉我们，在 VM 上分配给缓存 IOPS 的 16,000 IOPS 中，使用了 61%。 此百分比意味着存储 IO 瓶颈与缓存的磁盘无关，因为该指标值没有达到 100%。 我们现在来看一下“已使用的 VM 未缓存 IOPS 的百分比”指标：

![显示“已使用的 VM 未缓存 IOPS 的百分比”的屏幕截图。](media/vm-disk-performance/utilization-metrics-example/vm-uncached.jpg)

此指标为 100%。 此指标告诉我们，在 VM 上分配给未缓存 IOPS 的所有 12,800 IOPS 均已使用。 可以修正此问题的一种方法是，将 VM 大小更改为可处理更多 IO 的更大的大小。 但在执行此操作之前，我们先来看一下附加的磁盘，以了解这些磁盘发生了多少 IOPS。 通过查看“已使用的 OS 磁盘 IOPS 的百分比”来看一下 OS 磁盘：

![显示“已使用的 OS 磁盘 IOPS 的百分比”的屏幕截图。](media/vm-disk-performance/utilization-metrics-example/os-disk.jpg)

此指标告诉我们，在为此 P30 OS 磁盘预配的 5,000 IOPS 中，使用了大约 90%。 此百分比意味着 OS 磁盘上没有瓶颈。 现在我们通过查看 **已使用的数据磁盘 IOPS 的百分比** 来看一下附加到 VM 的数据磁盘：

![显示“已使用的数据磁盘 IOPS 的百分比”的屏幕截图。](media/vm-disk-performance/utilization-metrics-example/data-disks-no-splitting.jpg)

此指标告诉我们，在所有附加的磁盘上，平均已使用的 IOPS 百分比约为 42%。 此百分比是根据由这些磁盘使用且未从主机缓存中予以服务的 IOPS 计算得出的。 让我们通过对这些指标应用拆分并按 LUN 值拆分来深入了解此指标：

![显示拆分后“已使用的数据磁盘 IOPS 的百分比”的屏幕截图。](media/vm-disk-performance/utilization-metrics-example/data-disks-splitting.jpg)

此指标告诉我们，LUN 3 和 2 上附加的数据磁盘使用了它们约 85% 的预配 IOPS。 下面是 VM 和磁盘体系结构中 IO 情况的示意图：

![存储 IO 指标示例的示意图。](media/vm-disk-performance/utilization-metrics-example/metrics-diagram.jpg)

<!-- Update_Description: new article about virtual machine disk performance 3 -->
<!--NEW.date: 11/02/2020-->