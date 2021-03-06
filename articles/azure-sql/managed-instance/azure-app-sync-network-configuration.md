---
title: 同步 Azure 应用服务的网络配置
titleSuffix: Azure SQL Managed Instance
description: 本文讨论如何将 Azure 应用程序服务托管计划的网络配置与 Azure SQL 托管实例同步。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: sstein, bonova, carlrab
origin.date: 12/13/2018
ms.date: 07/13/2020
ms.openlocfilehash: 54d226886735c9887529dc81b061873522bad781
ms.sourcegitcommit: fa26665aab1899e35ef7b93ddc3e1631c009dd04
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2020
ms.locfileid: "86227282"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan-with-azure-sql-managed-instance"></a>将 Azure 应用服务托管计划的网络配置与 Azure SQL 托管实例同步
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

虽然[已将应用与 Azure 虚拟网络集成](../../app-service/web-sites-integrate-with-vnet.md)，仍可能出现无法建立与 SQL 托管实例的连接的情况。 刷新或同步服务计划的网络配置可以解决此问题。 

## <a name="sync-network-configuration"></a>同步网络配置 

为此，请执行以下步骤：  

1. 转到 Web 应用应用服务计划。

   ![应用服务计划的屏幕截图](./media/azure-app-sync-network-configuration/app-service-plan.png)

2. 选择“网络”，然后选择“单击此处进行管理” 。

   ![管理服务计划的屏幕截图](./media/azure-app-sync-network-configuration/manage-plan.png)

3. 选择“VNet”，并单击“同步网络” 。

   ![同步网络的屏幕截图](./media/azure-app-sync-network-configuration/sync.png)

4. 请等待同步完成。
  
   ![已完成同步的屏幕截图](./media/azure-app-sync-network-configuration/sync-done.png)

现在可尝试重新建立与 SQL 托管实例的连接。

## <a name="next-steps"></a>后续步骤

- 有关为 SQL 托管实例配置 VNet 的信息，请参阅 [SQL 托管实例 VNet 体系结构](connectivity-architecture-overview.md)和[如何配置现有 VNet](vnet-existing-add-subnet.md)。
