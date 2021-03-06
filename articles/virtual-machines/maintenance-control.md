---
title: 使用 Azure 门户对 Azure 虚拟机进行维护控制概述
description: 了解如何使用维护控制来控制何时向 Azure VM 应用维护。
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
origin.date: 11/19/2020
author: rockboyfor
ms.date: 01/04/2021
ms.testscope: no
ms.testdate: ''
ms.author: v-yeche
ms.openlocfilehash: a2c3accc9305502730f190a51f68d114a0008105
ms.sourcegitcommit: b4fd26098461cb779b973c7592f951aad77351f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97857130"
---
# <a name="managing-platform-updates-with-maintenance-control"></a>使用维护控制管理平台更新 

使用维护控制管理不需要重启的平台更新。 Azure 会频繁更新其基础结构，以提高可靠性、性能、安全性或推出新功能。 大多数更新对用户是透明的。 一些敏感的工作负载，如游戏、媒体流和金融交易，甚至不能容忍 VM 冻结或断开连接几秒钟来进行维护。 维护控制允许你等待平台更新并在 35 天滚动时段内应用它们。 

维护控制允许你决定何时向独立 VM 和 Azure 专用主机应用更新。

使用维护控制，你可以：
- 将许多更新作为一个更新包进行批处理。
- 等待长达 35 天才应用更新。 
- 通过配置维护计划或使用 [Azure Functions](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler)，可以自动执行平台更新。
- 维护配置跨订阅和资源组生效。 

## <a name="limitations"></a>限制

- VM 必须位于[专用主机](./dedicated-hosts.md)上，或者必须使用[独立 VM 大小](isolation.md)进行创建。
- 如果已声明维护计划，则必须至少维持 2 个小时。
- 35 天后，更新将自动应用。
- 用户必须具有“资源参与者”  访问权限。

## <a name="management-options"></a>管理选项

可以使用以下任一选项来创建和管理维护配置：

- [Azure CLI](maintenance-control-cli.md)
- [Azure PowerShell](maintenance-control-powershell.md)
- [Azure 门户](maintenance-control-portal.md)

有关 Azure Functions 示例，请参阅[使用维护控制和 Azure Functions 来计划维护更新](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler)。

## <a name="next-steps"></a>后续步骤

若要了解详细信息，请参阅[维护和更新](maintenance-and-updates.md)。

<!-- Update_Description: update meta properties, wording update, update link -->