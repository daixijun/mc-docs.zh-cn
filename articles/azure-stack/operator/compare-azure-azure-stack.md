---
title: 全球 Azure、Azure Stack Hub 与 Azure Stack HCI 之间的差异
titleSuffix: Azure Stack Hub
description: 了解全球 Azure、Azure Stack Hub 与 Azure Stack HCI 之间的差异。
author: WenJason
ms.topic: overview
origin.date: 07/10/2019
ms.date: 01/11/2021
ms.author: v-jay
ms.reviewer: unknown
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: 584e1d3a028c0310c46532269255cac5fa9448a3
ms.sourcegitcommit: 3f54ab515b784c9973eb00a5c9b4afbf28a930a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97894384"
---
# <a name="differences-between-azure-azure-stack-hub"></a>Azure 与 Azure Stack Hub 之间的差异

Microsoft 在一个 Azure 生态系统中提供 Azure 和 Azure Stack Hub 系列的服务。 无论企业使用 Azure 还是本地资源，都可以结合 Azure 资源管理器使用相同的应用程序模型、自助门户和 API 来提供基于云的功能。

本文介绍 Azure 和 Azure Stack Hub 功能之间的差异。 它提供了常见的方案建议，帮助你在为组织交付基于 Microsoft 云的服务时做出最佳选择。

![Azure 生态系统概述](./media/compare-azure-azure-stack/azure-family-updated.png)

## <a name="azure"></a>Azure

Azure 包括一系列不断扩增的云服务，可帮助组织解决业务难题。 它允许你自由使用偏好的工具和框架，在大规模全球网络中构建、管理和部署应用。

Azure 提供 100 多个可在 4 个区域使用的服务。 有关 Azure 服务的最新列表，请参阅 [*各区域可用的产品*](https://azure.microsoft.com/global-infrastructure/services/?regions=china-non-regional,china-east,china-east-2,china-north,china-north-2&products=all)。 Azure 中提供的服务按类别列出，同时会指出是否已推出正式版，或者以预览版提供。

有关全球 Azure 服务的详细信息，请参阅 [Azure 入门](https://docs.azure.cn/#pivot=get-started&panel=get-started1)。

## <a name="azure-stack-hub"></a>Azure Stack Hub

Azure Stack Hub 是 Azure 的扩展，它将云计算的敏捷性和创新引入到了本地环境。 可以使用部署在本地的 Azure Stack Hub 来提供 Azure 一致性服务，这些服务可以连接到 Internet（和 Azure），也可以位于未连接 Internet 的离线环境中。 Azure Stack Hub 使用的底层技术与 Stack 相同，包括基础结构即服务 (IaaS)、软件即服务 (SaaS) 和可选平台即服务 (PaaS) 功能的核心组件。 这些功能包括：

- 适用于 Windows 和 Linux 的 Azure VM
- Azure Web 应用和 Functions
- Azure Key Vault
- Azure Resource Manager
- Azure 市场
- 容器
- 管理工具（计划、套餐、RBAC 等）

Azure Stack Hub 的 PaaS 功能是可选的，因为 Azure Stack Hub 不是由 Microsoft 操作，而是由客户操作。 这意味着，如果你已准备好从最终用户抽离底层基础结构和流程，则可以向最终用户提供任何 PaaS 服务。 但是，Azure Stack Hub 包含多个可选的 PaaS 服务提供程序，包括应用服务、SQL 数据库和 MySQL 数据库。 这些产品以资源提供程序的形式交付，因此已经是多租户性质、会通过标准 Azure Stack Hub 更新不断更新、显示在 Azure Stack Hub 门户中，并与 Azure Stack Hub 妥善集成。

除上述资源提供程序以外，还有其他 PaaS 服务可供使用，经测试它们可以用作在 IaaS 中运行的[基于 Azure 资源管理器模板的解决方案](https://github.com/Azure/AzureStack-QuickStart-Templates)。 Azure Stack Hub 操作员可将其作为 PaaS 服务提供给用户，这些服务包括：

- Service Fabric
- Kubernetes 容器服务
- 以太坊区块链
- Cloud Foundry

### <a name="example-use-cases-for-azure-stack-hub"></a>Azure Stack Hub 的示例用例

- 财务建模
- 临床诊断和理赔数据
- IoT 设备分析
- 零售分类优化
- 供应链优化
- 工业 IoT
- 预见性维护
- 智能城市
- 公民参与

若要详细了解 Azure Stack Hub，请参阅[什么是 Azure Stack Hub](azure-stack-overview.md)。

## <a name="next-steps"></a>后续步骤

[Azure Stack Hub 管理基础知识](azure-stack-manage-basics.md)

[快速入门：使用 Azure Stack Hub 管理门户](azure-stack-manage-portals.md)
