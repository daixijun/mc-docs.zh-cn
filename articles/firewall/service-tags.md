---
title: Azure 防火墙服务标记概述
description: 服务标记表示一组 IP 地址前缀，帮助最大程度地降低安全规则创建过程的复杂性。
services: firewall
ms.service: firewall
ms.topic: article
origin.date: 11/19/2019
author: rockboyfor
ms.date: 01/11/2021
ms.author: v-yeche
ms.openlocfilehash: d082644386e264b458664a598caf4a5b0377b37a
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98021326"
---
# <a name="azure-firewall-service-tags"></a>Azure 防火墙服务标记

服务标记表示一组 IP 地址前缀，帮助最大程度地降低安全规则创建过程的复杂性。 无法创建自己的服务标记，也无法指定要将哪些 IP 地址包含在标记中。 Azure 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

Azure 防火墙服务标记可用于网络规则目标字段。 它们可用于代替特定的 IP 地址。

## <a name="supported-service-tags"></a>支持的服务标记

有关可在 Azure 防火墙网络规则中使用的服务标记的列表，请参阅[虚拟网络服务标记](../virtual-network/service-tags-overview.md#available-service-tags)。

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure 防火墙规则，请参阅 [Azure 防火墙规则处理逻辑](rule-processing.md)。

<!-- Update_Description: update meta properties, wording update, update link -->