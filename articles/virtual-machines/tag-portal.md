---
title: 如何使用 Azure 门户标记 VM
description: 了解如何使用 Azure 门户标记虚拟机。
ms.topic: how-to
ms.workload: infrastructure-services
ms.service: virtual-machines
origin.date: 11/11/2020
author: rockboyfor
ms.date: 01/04/2021
ms.testscope: yes
ms.testdate: 11/30/2020
ms.author: v-yeche
ms.openlocfilehash: 58e1df1b940b01aa23102f3d76f6a4c572479368
ms.sourcegitcommit: b4fd26098461cb779b973c7592f951aad77351f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97857152"
---
<!--Verified successfully-->
# <a name="tagging-a-vm-using-the-portal"></a>使用门户标记 VM

本文介绍如何使用门户将标记添加到 VM。 标记是用户定义的键/值对，可直接放置在资源或资源组中。 针对每个资源和资源组，Azure 当前支持最多 50 个标记。 标记可以在创建时放置在资源中或添加到现有资源中。

1. 在门户中导航到你的 VM。
1. 在“概要”中，选择“单击此处以添加标记”。

    :::image type="content" source="media/tag/azure-portal-tag.png" alt-text="VM 页的“概要”部分的屏幕截图。":::

1. 为“名称”和“值”添加值，然后选择“保存”。

    :::image type="content" source="media/tag/key-value.png" alt-text="用于将键值对添加为标记的页的屏幕截图。":::

后续步骤

- 若要详细了解如何标记 Azure 资源，请参阅 [Azure 资源管理器概述](../azure-resource-manager/management/overview.md)和 [使用标记来组织 Azure 资源](../azure-resource-manager/management/tag-resources.md)。

<!--Not Available on [Understanding your Azure Bill](../cost-management-billing/understand/review-individual-bill.md)-->
<!--Not Available on [Gain insights into your Azure resource consumption](../cost-management-billing/manage/usage-rate-card-overview.md)-->

<!-- Update_Description: update meta properties, wording update, update link -->