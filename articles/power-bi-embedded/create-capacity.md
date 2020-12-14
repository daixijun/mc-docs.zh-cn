---
title: 在 Azure 门户中创建 Power BI Embedded 容量 | Microsoft Docs
description: 本文详述了如何在 Azure 中创建 Power BI Embedded 容量。
author: markingmyname
manager: kfile
ms.author: v-junlch
ms.service: power-bi-embedded
ms.component: ''
ms.devlang: csharp, javascript
ms.topic: overview
ms.reviewer: ''
origin.date: 05/31/2018
ms.date: 06/12/2020
ms.openlocfilehash: b94f263f6b32214fb12e01df28e7bc20fae0fc65
ms.sourcegitcommit: f436acd1e2a0108918a6d2ee9a1aac88827d6e37
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509018"
---
# <a name="create-power-bi-embedded-capacity-in-the-azure-portal"></a>在 Azure 门户中创建 Power BI Embedded 容量

本文详述了如何在 Azure 中创建 Power BI Embedded 容量。 Power BI Embedded 有助于在应用中快速添加无与伦比的视觉对象、报表和仪表板，从而简化了 Power BI 容量。

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

## <a name="before-you-begin"></a>准备阶段

若要完成本快速入门，需要以下项：

- **Azure 订阅：** 访问 [Azure 试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)以创建帐户。
- **Azure Active Directory：** 订阅必须与 Azure Active Directory (AAD) 租户相关联。 此外，需要使用相应租户中的帐户登录 Azure*_。 不支持 Microsoft 帐户。 若要了解详细信息，请参阅[身份验证和用户权限](../analysis-services/analysis-services-manage-users.md)。
- Power BI 租户：你的 AAD 租户中必须至少有一个帐户已注册了 Power BI。
- **资源组：** 使用已有资源组，或 [创建新资源组](../azure-resource-manager/resource-group-overview.md)。

## <a name="create-a-capacity"></a>创建容量

1. 登录到 [Azure 门户](https://portal.azure.cn/)。

2. 选择“创建资源” > “数据 + 分析”。

3. 在搜索框中，搜索“Power BI Embedded”。

4. 在 Power BI Embedded 中，选择“创建”。

5. 填写所需信息，然后选择“创建”。

    ![创建新容量时要填写的字段](./media/create-capacity/azure-portal-create-power-bi-embedded.png)

    |设置 |说明 |
    |---------|---------|
    |**资源名称**|用于标识容量的名称。 除了显示在 Azure 门户中之外，资源名称还显示在 Power BI 管理门户中。|
    |**订阅**|要为其创建容量的订阅。|
    |**资源组**|包含此新容量的资源组。 从现有资源组中进行选取，或者另外创建一个。 有关详细信息，请参阅 [Azure Resource Manager 概述](../azure-resource-manager/resource-group-overview.md)。|
    |**Power BI 容量管理员**|Power BI 容量管理员可以在 Power BI 管理门户中查看容量并向其他用户分配权限。 默认情况下，容量管理员是你的帐户。 容量管理员必须在你的 Power BI 租户内。|
    |**位置**|为你的租户托管 Power BI 的位置。 此设置会自动解析，你的默认位置为你的主区域。|
    |**定价层**|选择满足你的需求的 SKU（V 核心计数和内存大小）。  有关详细信息，请参阅 [Power BI Embedded 定价](https://www.azure.cn/pricing/details/power-bi-embedded/)|

6. 选择“创建” 。

创建过程通常不超过一分钟，一般几秒便可完成。 如果选择了“固定到仪表板”，可导航到仪表板来查看新容量。 或者，可导航到“所有服务” > “Power BI Embedded”来查看容量是否已准备就绪。 

![其中显示了 Power BI Embedded 容量的 Azure 门户仪表板](./media/create-capacity/azure-portal-dashboard.png)

## <a name="next-steps"></a>后续步骤

若要使用新的 Power BI Embedded 容量，请浏览到 Power BI 管理门户来分配工作区。 有关详细信息，请参阅[管理 Power BI Premium 和 Power BI Embedded 中的容量](https://powerbi.microsoft.com/documentation/powerbi-admin-premium-manage/)。

> [!NOTE]
> Premium 容量在 Power BI China 尚不完全可用。

如果不需要使用此容量，可将其暂停以停止计费。 有关详细信息，请参阅[在 Azure 门户中暂停和启动 Power BI Embedded 容量](pause-start.md)。

若要开始在应用程序中嵌入 Power BI 内容，请参阅[如何嵌入 Power BI 仪表板、报表和磁贴](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/)。

有更多问题？ [尝试在 Power BI 社区中提问](http://community.powerbi.com/)

