---
title: 托管磁盘突发
description: 了解 Azure 磁盘和 Azure 虚拟机的磁盘突发。
origin.date: 09/22/2020
author: rockboyfor
ms.date: 11/16/2020
ms.testscope: no
ms.testdate: 11/16/2020
ms.author: v-yeche
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 5caf399e8472dacabd1ab17b80f12d180b8d6398
ms.sourcegitcommit: 39288459139a40195d1b4161dfb0bb96f5b71e8e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94590950"
---
<!--Verified successfully from renamed articles-->
<!--CONFIRM THE DEOPLOMENT REGIONS BEFORE RELEASEMENT-->
# <a name="managed-disk-bursting"></a>托管磁盘突发
[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting.md)]

<!--Not Avaialble on ## Virtual Machine level bursting-->

<!--Not Available on - [Lsv2-series](lsv2-series.md)-->

## <a name="disk-level-bursting"></a>磁盘级别突发
在 Azure 公有云、政府云和中国云的所有区域中，还为磁盘大小为 P20 和更小的[高级 SSD](disks-types.md#premium-ssd) 提供了突发功能。 在支持磁盘突发的磁盘大小的所有新的和现有的部署上，默认已启用磁盘突发。 

[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting-2.md)]

<!-- Update_Description: new article about disk bursting -->
<!--NEW.date: 11/16/2020-->