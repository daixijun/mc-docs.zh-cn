---
title: 使用 Azure 资源转移器从移动集合中删除资源
description: 了解如何使用 Azure 资源转移器从移动集合中删除资源。
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 11/30/2020
ms.author: raynew
ms.openlocfilehash: 13cc2c1ab4a4a227ca21d35f59d4fda431aa3495
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024026"
---
# <a name="manage-move-collections-and-resource-groups"></a>管理移动集合和资源组

本文介绍如何使用 [Azure 资源转移器](overview.md)移动集合中删除资源，或删除移动集合/资源组。 在 Azure 区域之间移动 Azure 资源时会使用移动集合。

## <a name="remove-a-resource-portal"></a>删除资源（门户）

可以从资源转移器门户删除移动集合中的资源，如下所示：

1. 在“跨区域”中，选择想要从集合中删除的所有资源，然后选择“删除” 。 

    ![用于选择删除的按钮](./media/remove-move-resources/portal-select-resources.png)

2. 在“删除资源”中，单击“删除” 。

    ![用于选择从移动集合中删除资源的按钮](./media/remove-move-resources/remove-portal.png)

## <a name="remove-a-move-collectionresource-group-portal"></a>删除移动集合/资源组（门户）

可以在门户中删除移动集合/资源组。

1. 按照上述过程中的说明从集合中删除资源。 在删除资源组时，请确保它不包含任何资源。
2. 删除移动集合或资源组。  

## <a name="remove-a-resource-powershell"></a>删除资源 (PowerShell)

使用 PowerShell 从集合中删除资源（我们以 PSDemoVM 计算机为例），如下所示：

```azurepowershell
# Remove a resource using the resource ID
Remove-AzResourceMoverMoveResource -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-chinaeast-chinanorth  -MoveCollectionName MoveCollection-chinaeast-chinanorth -Name PSDemoVM
```
**预期输出**

![从移动集合中删除资源后的输出文本](./media/remove-move-resources/remove-resource.png)

## <a name="remove-a-collection-powershell"></a>删除集合 (PowerShell)

使用 PowerShell 删除整个移动集合，如下所示：

1. 按照上述说明，使用 PowerShell 从集合中删除资源。
2. 运行：

    ```azurepowershell
    # Remove a resource using the resource ID
    Remove-AzResourceMoverMoveCollection -SubscriptionId <subscription-id> -ResourceGroupName RegionMoveRG-chinaeast-chinanorth -MoveCollectionName MoveCollection-chinaeast-chinanorth
    ```
    **预期输出**
    
    ![删除移动集合后的输出文本](./media/remove-move-resources/remove-collection.png)

## <a name="vm-resource-state-after-removing"></a>删除之后的 VM 资源状态

从移动集合中删除 VM 资源时发生的情况取决于资源状态，如表中所述。

###  <a name="remove-vm-state"></a>删除 VM 状态
**资源状态** | **VM** | **联网**
--- | --- | --- 
**已添加到移动集合** | 从移动集合中删除。 | 从移动集合中删除。 
**依赖关系已解决/准备挂起** | 从移动集合中删除  | 从移动集合中删除。 
**正在进行准备**<br/> （或正在进行的任何其他状态） | 删除操作失败，出现错误。  | 删除操作失败，出现错误。
**准备失败** | 从移动集合中删除。<br/>删除在目标区域中创建的任何内容，包括副本磁盘。 <br/><br/> 在移动过程中创建的基础结构资源需要手动删除。 | 从移动集合中删除。  
**启动移动挂起** | 从移动集合中删除。<br/><br/> 删除在目标区域中创建的任何内容，包括 VM、副本磁盘等。  <br/><br/> 在移动过程中创建的基础结构资源需要手动删除。 | 从移动集合中删除。
**启动移动失败** | 从移动集合中删除。<br/><br/> 删除在目标区域中创建的任何内容，包括 VM、副本磁盘等。  <br/><br/> 在移动过程中创建的基础结构资源需要手动删除。 | 从移动集合中删除。
**提交挂起** | 建议放弃移动以便先删除目标资源。<br/><br/> 资源会恢复到“启动移动挂起”状态，你可以从此处继续。 | 建议放弃移动以便先删除目标资源。<br/><br/> 资源会恢复到“启动移动挂起”状态，你可以从此处继续。 
**提交失败** | 建议放弃，以便先删除目标资源。<br/><br/> 资源会恢复到“启动移动挂起”状态，你可以从此处继续。 | 建议放弃移动以便先删除目标资源。<br/><br/> 资源会恢复到“启动移动挂起”状态，你可以从此处继续。
**放弃完成** | 资源会恢复到“启动移动挂起”状态。<br/><br/> 资源已从移动集合中删除，另外被删除的还包括在 VM、副本磁盘、保管库等目标中创建的任何内容。  <br/><br/> 在移动过程中创建的基础结构资源需要手动删除。 <br/><br/> 在移动过程中创建的基础结构资源需要手动删除。 |  资源会恢复到“启动移动挂起”状态。<br/><br/> 资源已从移动集合中删除。
**放弃失败** | 建议放弃移动以便先删除目标资源。<br/><br/> 之后，资源会恢复到“启动移动挂起”状态，你可以从此处继续。 | 建议放弃移动以便先删除目标资源。<br/><br/> 之后，资源会恢复到“启动移动挂起”状态，你可以从此处继续。
**删除源挂起** | 已从移动集合中删除。<br/><br/> 未删除在目标区域中创建的任何内容。  | 已从移动集合中删除。<br/><br/> 未删除在目标区域中创建的任何内容。
**删除源失败** | 已从移动集合中删除。<br/><br/> 未删除在目标区域中创建的任何内容。 | 已从移动集合中删除。<br/><br/> 未删除在目标区域中创建的任何内容。

## <a name="sql-resource-state-after-removing"></a>删除之后的 SQL 资源状态

从移动集合中删除 Azure SQL 资源时发生的情况取决于资源状态，如表中所述。

**资源状态** | **SQL** 
--- | --- 
**已添加到移动集合** | 从移动集合中删除。 
**依赖关系已解决/准备挂起** | 从移动集合中删除 
**正在进行准备**<br/> （或正在进行的任何其他状态）  | 删除操作失败，出现错误。 
**准备失败** | 从移动集合中删除<br/><br/>删除在目标区域中创建的任何内容。 
**启动移动挂起** |  从移动集合中删除<br/><br/>删除在目标区域中创建的任何内容。 此时，SQL 数据库存在，并且将被删除。 
**启动移动失败** | 从移动集合中删除<br/><br/>删除在目标区域中创建的任何内容。 此时，SQL 数据库存在，并且必须被删除。 
**提交挂起** | 建议放弃移动以便先删除目标资源。<br/><br/> 资源会恢复到“启动移动挂起”状态，你可以从此处继续。
**提交失败** | 建议放弃移动以便先删除目标资源。<br/><br/> 资源会恢复到“启动移动挂起”状态，你可以从此处继续。 
**放弃完成** |  资源会恢复到“启动移动挂起”状态。<br/><br/> 资源已从移动集合中删除，另外被删除的还有在目标中创建的任何内容，包括 SQL 数据库。 
**放弃失败** | 建议放弃移动以便先删除目标资源。<br/><br/> 之后，资源会恢复到“启动移动挂起”状态，你可以从此处继续。 
**删除源挂起** | 已从移动集合中删除。<br/><br/> 未删除在目标区域中创建的任何内容。 
**删除源失败** | 已从移动集合中删除。<br/><br/> 未删除在目标区域中创建的任何内容。 

## <a name="next-steps"></a>后续步骤

尝试使用资源转移器[将 VM 移动到](tutorial-move-region-virtual-machines.md)另一个区域。
