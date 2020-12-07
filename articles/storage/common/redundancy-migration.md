---
title: 更改存储帐户的复制方式
titleSuffix: Azure Storage
description: 了解如何更改现有存储帐户中数据的复制方式。
services: storage
author: WenJason
ms.service: storage
ms.topic: how-to
origin.date: 09/24/2020
ms.date: 11/30/2020
ms.author: v-jay
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 121b10370e5f116121524ec1a321f82c649ae279
ms.sourcegitcommit: dabbf66e4507a4a771f149d9f66fbdec6044dfbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96153107"
---
# <a name="change-how-a-storage-account-is-replicated"></a>更改存储帐户的复制方式

Azure 存储始终会存储数据的多个副本，以防范各种计划内和计划外的事件，包括暂时性的硬件故障、网络中断或断电、大范围自然灾害等。 即使面临故障时，冗余也可确保存储帐户满足 [Azure 存储的服务级别协议 (SLA)](https://www.azure.cn/support/sla/storage/index.html)的要求。

Azure 存储提供以下类型的复制：

- 本地冗余存储 (LRS)
- 异地冗余存储 (GRS) 或读取访问异地冗余存储 (RA-GRS)

有关这些选项每一个的概述，请参阅 [Azure 存储冗余](storage-redundancy.md)。

## <a name="switch-between-types-of-replication"></a>在复制类型之间切换

可以将存储帐户从一种类型的复制切换到任何其他类型，但某些方案比另一些方案更直接。 若要添加或删除对次要区域的异地复制或读取访问权限，可以使用 Azure 门户、PowerShell 或 Azure CLI 来更新复制设置。

下表概述了如何从每种复制类型切换到另一种复制类型：

| 切换 | …到 LRS | …到 GRS/RA-GRS |
|--------------------|----------------------------------------------------|---------------------------------------------------------------------|
| <b>…从 LRS</b> | 空值 | 使用 Azure 门户、PowerShell 或 CLI 更改复制设置<sup>1</sup> |
| <b>…从 GRS/RA-GRS</b> | 使用 Azure 门户、PowerShell 或 CLI 更改复制设置 | 空值 |

<sup>1</sup> 会产生一次性出口费用。

> [!CAUTION]
> 如果你为 (RA-)GRS 执行了[帐户故障转移](storage-disaster-recovery-guidance.md)，则在故障转移后该帐户在新的主要区域中是本地冗余的。 

## <a name="change-the-replication-setting"></a>更改复制设置

只要不更改在主要区域中复制数据的方式，就可以使用 Azure 门户、PowerShell 或 Azure CLI 更改存储帐户的复制设置。

更改存储帐户的复制方式不会导致应用程序停机。

# <a name="portal"></a>[Portal](#tab/portal)

若要在 Azure 门户中更改存储帐户的冗余选项，请执行以下步骤：

1. 导航到 Azure 门户中的存储帐户。
1. 选择 **配置** 设置。
1. 更新 **复制** 设置。

![屏幕截图，其中显示了如何在门户中更改复制选项](media/redundancy-migration/change-replication-option.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要通过 PowerShell 更改存储帐户的冗余选项，请调用 [Set-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) 命令并指定 `-SkuName` 参数：

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage_account> `
    -SkuName <sku>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要通过 Azure CLI 更改存储帐户的冗余选项，请调用 [az storage account update](/cli/storage/account#az-storage-account-update) 命令并指定 `--sku` 参数：

```azurecli
az storage account update \
    --name <storage-account>
    --resource-group <resource_group> \
    --sku <sku>
```

---

## <a name="costs-associated-with-changing-how-data-is-replicated"></a>与更改数据复制方式相关的成本

与更改数据复制方式相关的成本取决于你的转换路径。 从最便宜到最昂贵的 Azure 存储冗余产品/服务依次为 LRS、GRS 和 RA-GRS。

例如，从 LRS 转移到任何其他类型的复制会产生额外的费用，因为这是转移到更高级的冗余级别。 迁移到 GRS 或 RA-GRS 会产生出口带宽费用，因为（主要区域中的）数据将复制到远程次要区域。 此费用是在初始设置期间收取的一次性费用。 复制数据后，无需进一步支付迁移费用。 有关带宽费用的详细信息，请参阅 [Azure 存储定价页面](https://azure.cn/pricing/details/storage/blobs/)。

如果将存储帐户从 GRS 迁移到 LRS，则不会产生额外的费用，但从次要位置复制的数据将被删除。

> [!IMPORTANT]
> 如果将存储帐户从 RA-GRS 迁移到 GRS 或 LRS，则会在完成转换 30 天后以 RA-GRS 方式对该帐户计费。

## <a name="see-also"></a>另请参阅

- [Azure 存储冗余](storage-redundancy.md)
- [检查存储帐户的“上次同步时间”属性](last-sync-time-get.md)
- [使用异地冗余设计高度可用的应用程序](geo-redundant-design.md)
