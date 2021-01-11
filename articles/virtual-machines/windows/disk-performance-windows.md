---
title: 虚拟机和磁盘性能 - Windows
description: 详细了解如何在 Windows 上组合使用虚拟机及其附加的磁盘以提高性能。
origin.date: 10/12/2020
author: rockboyfor
ms.date: 01/04/2021
ms.testscope: no
ms.testdate: 10/19/2020
ms.author: v-yeche
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: ab96063c0faa7db889d04511e6284424de11e363
ms.sourcegitcommit: b4fd26098461cb779b973c7592f951aad77351f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97857159"
---
<!--Notice: Three Includes File in the new file-->
# <a name="virtual-machine-and-disk-performance-windows"></a>虚拟机和磁盘性能 (Windows)
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>虚拟机非缓存限制与缓存限制
 同时启用了高级存储和高级存储缓存的虚拟机有两种不同的存储带宽限制。 接下来，让我们继续查看 Standard_D8s_v3 虚拟机的示例。 下面是有关 [Dsv3 系列](../dv3-dsv3-series.md)及其中的 Standard_D8s_v3 的文档：

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

我们来对此 VM 和磁盘组合运行基准测试，这将创建 IO 活动，你可以在[此处](disks-benchmarks.md)了解如何在 Azure 上对存储 IO 进行基准测试。 通过基准测试工具，可以看到 VM 和磁盘组合能够实现 22,800 IOPS：

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]

<!-- Update_Description: update meta properties, wording update, update link -->