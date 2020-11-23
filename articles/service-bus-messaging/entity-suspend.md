---
title: Azure 服务总线 - 暂停消息实体
description: 本文介绍如何暂时暂停和重新激活 Azure 服务总线消息实体（队列、主题和订阅）。
ms.topic: article
origin.date: 09/29/2020
author: rockboyfor
ms.date: 11/16/2020
ms.testscope: no
ms.testdate: ''
ms.author: v-yeche
ms.openlocfilehash: 7322d098a286b49f46386981a95d0ccdbf6457b6
ms.sourcegitcommit: 39288459139a40195d1b4161dfb0bb96f5b71e8e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94590831"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>暂停（禁用）和重新激活消息实体

可以暂停队列、主题和订阅。 暂停后，实体处于禁用状态，其中所有消息都保留在存储中。 不过，无法删除或添加消息，相应的协议操作也会导致错误出现。

暂停实体通常是出于紧急管理方面的原因。 一种情况是，部署的接收程序发生故障，不仅会将消息从队列中剔除、无法处理消息，甚至还会错误地完成并删除消息。 如果诊断出这样的行为，就会禁用队列的接收行为，直到部署了更正后的代码，并且可以防止故障代码进一步加剧数据丢失问题为止。

暂停或重新激活可以由用户或系统执行。 系统只会出于重大管理原因（如达到订阅支出限制），才会暂停实体。 系统禁用的实体不能被用户重新激活，但在暂停原因消除后就会还原。

## <a name="queue-status"></a>队列状态 
可以为队列设置如下状态：

- **Active**：队列处于活动状态。
- **Disabled**：队列处于暂停状态。 它相当于同时设置 SendDisabled 和 ReceiveDisabled 。 
- **SendDisabled**：队列部分处于暂停状态，允许执行接收操作。
- **ReceiveDisabled**：队列部分处于暂停状态，允许执行发送操作。

### <a name="change-the-queue-status-in-the-azure-portal"></a>在 Azure 门户中更改队列状态： 

1. 在 Azure 门户中，导航到你的服务总线命名空间。 
1. 选择要更改其状态的队列。 可在底部的中间窗格中看到队列。 
1. 在“服务总线队列”页面上，将看到队列的当前状态显示为超链接。 如果未在左侧菜单中选择“概述”，请选择该选项以查看队列的状态。 选择队列的当前状态进行更改。 

    :::image type="content" source="./media/entity-suspend/select-state.png" alt-text="选择队列状态":::
4. 为队列选择新状态，然后选择“确定”。 

    :::image type="content" source="./media/entity-suspend/entity-state-change.png" alt-text="设置队列状态":::

门户只容许完全禁用队列。 也可以单独禁用发送和接收操作，方法是在 .NET Framework SDK 中使用服务总线 [NamespaceManager](https://docs.azure.cn/dotnet/api/microsoft.servicebus.namespacemanager) API，或通过 Azure CLI 或 Azure PowerShell 使用 Azure 资源管理器模板。

### <a name="change-the-queue-status-using-azure-powershell"></a>使用 Azure PowerShell 更改队列状态
下面的示例展示了用于禁用队列的 PowerShell 命令。 重新激活命令等同于以下示例，只需将 `Status` 设置为“Active”即可。

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

<!--Not Available on Topic Status-->
<!--Unable to change the Topic Status on MC Azure portal-->
 
## <a name="next-steps"></a>后续步骤

若要了解有关服务总线消息传送的详细信息，请参阅以下主题：

* [服务总线队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服务总线主题和订阅](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/entity-state-change.png

<!-- Update_Description: update meta properties, wording update, update link -->