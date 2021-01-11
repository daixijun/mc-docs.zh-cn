---
title: 如何使用模板来标记 VM
description: 了解如何使用模板来标记虚拟机。
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
origin.date: 10/26/2018
author: rockboyfor
ms.date: 01/04/2021
ms.testscope: yes|no
ms.testdate: 01/04/2021null
ms.author: v-yeche
ms.openlocfilehash: b673377e958cfc275d2592e064832596e726e69a
ms.sourcegitcommit: b4fd26098461cb779b973c7592f951aad77351f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97857680"
---
<!--Verified successfully-->
# <a name="tagging-a-vm-using-a-template"></a>使用模板来标记 VM

本文介绍如何使用资源管理器模板在 Azure 中标记 VM。 标记是用户定义的键/值对，可直接放置在资源或资源组中。 针对每个资源和资源组，Azure 当前支持最多 50 个标记。 标记可以在创建时放置在资源中或添加到现有资源中。

[此模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags)将标记放置在以下资源中：计算（虚拟机）、存储（存储帐户）和网络（公共 IP 地址、虚拟网络和网络接口）。 此模板适用于 Windows VM，但经过改造后也可用于 Linux VM。

单击 [模板链接](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) 中的 **部署至 Azure** 按钮。 此操作将导航到 [Azure 门户](https://portal.azure.cn/)，可在其中部署此模板。

:::image type="content" source="./media/tag/deploy-to-azure-tags.png" alt-text="使用标记进行简单部署":::

此模板包括以下标记：*Department*、*Application* 和 *Created By*。 如果想要不同的标记名称，则可以直接在模板中添加/编辑这些标记。

:::image type="content" source="./media/tag/azure-tags-in-a-template.png" alt-text="模板中的 Azure 标记":::

如你所见，标记将被定义为键值对，用冒号 (:) 分隔。 必须按以下格式定义标记：

```config
"tags": {
    "Key1" : "Value1",
    "Key2" : "Value2"
}
```

完成编辑后，使用选择的标记保存模板文件。

接下来，在“编辑参数”部分中，可以填写标记的值。 

:::image type="content" source="./media/tag/edit-tags-in-azure-portal.png" alt-text="通过 Azure 门户编辑标记":::

单击  “创建”使用标记值部署此模板。

后续步骤

- 若要详细了解如何标记 Azure 资源，请参阅 [Azure 资源管理器概述](../azure-resource-manager/management/overview.md)和 [使用标记来组织 Azure 资源](../azure-resource-manager/management/tag-resources.md)。

<!--Not Available on [Understanding your Azure Bill](../cost-management-billing/understand/review-individual-bill.md)-->
<!--Not Available on [Gain insights into your Azure resource consumption](../cost-management-billing/manage/usage-rate-card-overview.md)-->

<!-- Update_Description: new article about tag template -->
<!--NEW.date: 01/04/2021-->