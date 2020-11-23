---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/10/2020
title: 如何使用 Azure 防火墙为注入 VNet 的工作区分配单个公共 IP - Azure Databricks
description: 了解如何使用 Azure 防火墙为虚拟网络中的 Azure Databricks 工作区分配单个公共 IP 地址。
ms.openlocfilehash: 1501e9c2549083e67b788c6cc76005b2993388dc
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589778"
---
# <a name="how-to-assign-a-single-public-ip-for-vnet-injected-workspaces-using-azure-firewall"></a>如何使用 Azure 防火墙为注入了 VNet 的工作区分配单个公共 IP

可使用 Azure 防火墙创建[注入了 VNet 的工作区](/databricks/administration-guide/cloud-configurations/azure/vnet-inject)，其中所有群集都有一个单一 IP 出站地址。 该单一 IP 地址可用作允许基于特定 IP 地址进行访问的其他 Azure 服务和应用程序的额外安全层。

1. 在自己的虚拟网络中设置 Azure Databricks 工作区。
2. 在虚拟网络中设置防火墙。 请参阅[创建 NVA](/virtual-network/tutorial-create-route-table-portal#create-an-nva)。 创建防火墙时，应执行以下操作：
   * 记下防火墙的专用和公共 IP 地址，供以后使用。
   * 为公共子网[创建网络规则](/firewall/tutorial-firewall-deploy-portal#configure-a-network-rule)，以将所有流量转发到 Internet：
     * 名称: 任意名称
     * 优先级：100
     * 协议: 任何
     * 源地址:创建的虚拟网络中公共子网的 IP 范围
     * 目标地址:0.0.0.0/1
     * 目标端口: *
3. 创建自定义路由表，并将其与公共子网关联。
   1. 为以下服务添加自定义路由，该路由也称为用户定义路由 ([UDR](/virtual-network/virtual-networks-udr-overview#custom-routes))。 为你的区域指定 [Azure Databricks 区域地址](/databricks/administration-guide/cloud-configurations/azure/udr#control-plane-nat-and-webapp-ip-addresses)。 对于“下一个跃点类型”，输入 `Internet`，如[创建路由表](/virtual-network/tutorial-create-route-table-portal#create-a-route)中所示。
      * 控制平面 NAT VIP
      * Webapp
      * 元存储
      * 项目 Blob 存储
      * 日志 Blob 存储
   1. 使用以下值为防火墙添加自定义路由：
      * 地址前缀:0.0.0.0./0
      * 下一个跃点类型：虚拟设备
      * 下一个跃点地址:防火墙的专用 IP 地址。
   1. 将路由表与公共子网关联。
4. 验证设置
   1. 在 Azure Databricks 工作区中创建群集。
   1. 接下来，查询自己的路径中的 blob 存储或在单元中运行 `%fs ls`。
   1. 如果操作失败，请确认路由表具有所有必需的 UDR（包括服务终结点，而不是 Blob 存储的 UDR）

有关详细信息，请参阅[使用虚拟设备或防火墙路由 Azure Databricks 流量](/databricks/administration-guide/cloud-configurations/azure/on-prem-network#route-via-firewall)。