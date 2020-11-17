---
title: PowerShell cmdlet 参考
description: 了解 Azure 计划程序 PowerShell cmdlet
services: scheduler
ms.service: scheduler
author: WenJason
ms.author: v-jay
ms.reviewer: klam, estfan
ms.topic: article
origin.date: 08/18/2016
ms.date: 11/16/2020
ms.openlocfilehash: c9e74f9a22a5c6072e9f4c893841fec529bdf4ee
ms.sourcegitcommit: 39288459139a40195d1b4161dfb0bb96f5b71e8e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94590674"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Azure 计划程序 PowerShell cmdlet 参考

> [!IMPORTANT]
> [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)将替代[即将停用](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)的 Azure 计划程序。 若要继续使用在计划程序中设置的作业，请尽快[迁移到 Azure 逻辑应用](../scheduler/migrate-from-scheduler-to-logic-apps.md)。 
>
> 计划程序在 Azure 门户中不再可用，但 [REST API](https://docs.microsoft.com/rest/api/scheduler) 和 [Azure 计划程序 PowerShell cmdlet](scheduler-powershell-reference.md) 目前仍可用，以便你可以管理作业和作业集合。

若要编写脚本以创建和管理计划程序作业和作业集合，可以使用 PowerShell cmdlet。 本文列出了主要 Azure 计划程序 PowerShell cmdlet，以及指向其参考文章的链接。 要为 Azure 订阅安装 Azure PowerShell，请参阅[如何安装和配置 Azure PowerShell](https://docs.microsoft.com/powershell/azure/)。 有关 [Azure 资源管理器 cmdlet](https://docs.microsoft.com/powershell/azure/) 的详细信息，请参阅[将 Azure PowerShell 与 Azure 资源管理器配合使用](../azure-resource-manager/management/manage-resources-powershell.md)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

| Cmdlet | 说明 |
|--------|-------------|
| [Disable-AzSchedulerJobCollection](https://docs.microsoft.com/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |禁用某一作业集合。 |
| [Enable-AzureRmSchedulerJobCollection](https://docs.microsoft.com/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |启用某一作业集合。 |
| [Get-AzSchedulerJob](https://docs.microsoft.com/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |获取计划程序作业。 |
| [Get-AzSchedulerJobCollection](https://docs.microsoft.com/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |获取作业集合。 |
| [Get-AzSchedulerJobHistory](https://docs.microsoft.com/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |获取作业历史记录。 |
| [New-AzSchedulerHttpJob](https://docs.microsoft.com/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |创建 HTTP 作业。 |
| [New-AzSchedulerJobCollection](https://docs.microsoft.com/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |创建作业集合。 |
| [New-AzSchedulerServiceBusQueueJob](https://docs.microsoft.com/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | 创建服务总线队列作业。 |
| [New-AzSchedulerServiceBusTopicJob](https://docs.microsoft.com/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |创建服务总线主题作业。 |
| [New-AzSchedulerStorageQueueJob](https://docs.microsoft.com/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |创建存储队列作业。 |
| [Remove-AzSchedulerJob](https://docs.microsoft.com/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |删除计划程序作业。 |
| [Remove-AzSchedulerJobCollection](https://docs.microsoft.com/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |删除作业集合。 |
| [Set-AzSchedulerHttpJob](https://docs.microsoft.com/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |修改计划程序 HTTP 作业。 |
| [Set-AzSchedulerJobCollection](https://docs.microsoft.com/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |修改作业集合。 |
| [Set-AzSchedulerServiceBusQueueJob](https://docs.microsoft.com/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |修改服务总线队列作业。 |
| [Set-AzSchedulerServiceBusTopicJob](https://docs.microsoft.com/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |修改服务总线主题作业。 |
| [Set-AzSchedulerStorageQueueJob](https://docs.microsoft.com/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |修改存储队列作业。 |
||| 

有关详细信息，可以运行以下任何 cmdlet： 

```text
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="next-steps"></a>后续步骤

* [Azure 计划程序的概念、术语和实体层次结构](scheduler-concepts-terms.md)
* [Azure 计划程序的限制、默认值和错误代码](scheduler-limits-defaults-errors.md)
* [Azure 计划程序 REST API 参考](https://docs.microsoft.com/rest/api/scheduler)