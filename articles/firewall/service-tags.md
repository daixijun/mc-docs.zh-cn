---
title: Azure 防火墙服务标记概述
description: 服务标记表示一组 IP 地址前缀，帮助最大程度地降低安全规则创建过程的复杂性。
services: firewall
ms.service: firewall
ms.topic: article
origin.date: 11/19/2019
author: rockboyfor
ms.date: 12/07/2020
ms.author: v-yeche
ms.openlocfilehash: e901d384b123e2a731da8f33797f3bcbbfdf48ab
ms.sourcegitcommit: ac1cb9a6531f2c843002914023757ab3f306dc3e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2020
ms.locfileid: "96746710"
---
# <a name="azure-firewall-service-tags"></a>Azure 防火墙服务标记

服务标记表示一组 IP 地址前缀，帮助最大程度地降低安全规则创建过程的复杂性。 无法创建自己的服务标记，也无法指定要将哪些 IP 地址包含在标记中。 Azure 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

Azure 防火墙服务标记可用于网络规则目标字段。 它们可用于代替特定的 IP 地址。

## <a name="supported-service-tags"></a>支持的服务标记

有关可在 Azure 防火墙网络规则中使用的服务标记的列表，请参阅[安全组](../virtual-network/network-security-groups-overview.md#service-tags)。

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure 防火墙规则，请参阅 [Azure 防火墙规则处理逻辑](rule-processing.md)。

<!-- Update_Description: update meta properties, wording update, update link -->