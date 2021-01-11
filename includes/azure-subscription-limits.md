---
title: 包含文件
description: include 文件
author: rockboyfor
ms.service: azure-resource-manager
ms.topic: include
origin.date: 05/18/2018
ms.date: 01/11/2021
ms.testscope: no
ms.testdate: 05/18/2020
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 78249f8a907e9601dd8c82eea7e5f7bb33030f50
ms.sourcegitcommit: 415fb60a99f3ff239e38670f16e6daab021a675b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/28/2020
ms.locfileid: "97793440"
---
<!--NOT AVAILABLE ON [subscription](https://docs.azure.cn/billing/billing-recharge-an-existing-pia-subscription)-->
<!--CORRECT ON /billing/billing-add-change-azure-subscription-administrator-->

| 资源 | 默认限制 | 最大限制 |
| --- | --- | --- |
| 每个订阅的 vCPU 数量 <sup>1</sup> |20 |10,000 |
| 每个订阅的[协同管理员数](/billing/billing-add-change-azure-subscription-administrator) |200 |200 |
| 每个订阅的[存储帐户数](../articles/storage/common/storage-create-storage-account.md)<sup>2</sup> |100 |100 |
| 每个订阅的[云服务数](../articles/cloud-services/cloud-services-choose-me.md) |20 |200 |
| 每个订阅的[本地网络数](https://docs.microsoft.com/previous-versions/azure/reference/jj157100(v=azure.100)) |10 |500 |
| 每个订阅的 DNS 服务器 |9 |100 |
| 每个订阅的保留的 IP |20 |100 |
| 每个订阅的[地缘组数](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) |256 |256 |
| 订阅名称长度（字符） | 64 | 64 |

<sup>1</sup>特小实例作为一个 vCPU 至 vCPU 限制计数，即使使用了部分 CPU 核心。

<sup>2</sup>此存储帐户限制包括标准和高级存储帐户。

<!-- Update_Description: update meta properties, wording update, update link -->