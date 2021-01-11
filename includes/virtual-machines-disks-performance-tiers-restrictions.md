---
title: include 文件
description: include 文件
services: virtual-machines
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
ms.date: 11/20/2020
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 873907a641e860a708ed7427cf69adcc3319c5f8
ms.sourcegitcommit: b4fd26098461cb779b973c7592f951aad77351f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97856734"
---
<!--Verified successfully from rename articles-->
- 目前仅高级 SSD 支持此功能。
- 必须先对 VM 解除分配或从正在运行的 VM 中拆离磁盘，然后才能更改磁盘层级。
- P60、P70 和 P80 性能层只能由大于 4,096 GiB 的磁盘使用。
- 磁盘的性能层每 12 小时只能降级一次。