---
title: 用于网络隔离的 Azure 服务的虚拟网络集成
titlesuffix: Azure Virtual Network
description: 本文介绍了如何通过各种方法将 Azure 服务集成到虚拟网络，以便安全地访问 Azure 服务。
services: virtual-network
documentationcenter: na
manager: mtillman
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.workload: infrastructure-services
origin.date: 12/01/2020
author: rockboyfor
ms.date: 01/18/2021
ms.testscope: no
ms.testdate: 01/18/2021
ms.author: v-yeche
ms.openlocfilehash: 83640ce5d87977bc54ce947d605dd56facc6377e
ms.sourcegitcommit: c8ec440978b4acdf1dd5b7fda30866872069e005
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98231143"
---
<!--Verified successfully-->
# <a name="integrate-azure-services-with-virtual-networks-for-network-isolation"></a>将 Azure 服务与虚拟网络集成以实现网络隔离

使用 Azure 服务的虚拟网络 (VNet) 集成，你可以仅允许通过你的虚拟网络基础结构来访问服务。 VNet 基础结构还包括对等互连的虚拟网络和本地网络。

VNet 集成可以为 Azure 服务提供网络隔离的优点，可通过以下一种或多种方法实现：
- [将服务的专用实例部署到虚拟网络中](virtual-network-for-azure-services.md)。 随后即可在虚拟网络内以及从本地网络私密访问这些服务。
- 使用专用终结点，它可以通过私密且安全的方式将你连接到由 [Azure 专用链接](../private-link/private-link-overview.md)提供支持的服务。 专用终结点使用 VNet 中的专用 IP 地址将服务有效地引入虚拟网络中。

    <!--Not Available on [Private Endpoint](../private-link/private-endpoint-overview.md)-->
    
- 通过[服务终结点](virtual-network-service-endpoints-overview.md)将虚拟网络扩展到服务，使用公共终结点访问服务。 服务终结点可使服务资源在虚拟网络中得到保护。
- 使用[服务标记](service-tags-overview.md)来允许或拒绝 Azure 资源进出公共 IP 终结点的流量。

## <a name="deploy-dedicated-azure-services-into-virtual-networks"></a>将专用 Azure 服务部署到虚拟网络

在虚拟网络中部署专用 Azure 服务时，可通过专用 IP 地址与服务资源进行私密通信。

:::image type="content" source="./media/virtual-network-for-azure-services/deploy-service-into-vnet.png" alt-text="将专用 Azure 服务部署到虚拟网络":::

将专用 Azure 服务部署到虚拟网络可提供以下功能：
- 虚拟网络内的资源可以通过专用 IP 地址彼此进行私密通信。 例如，在虚拟网络中，在虚拟机上运行的 HDInsight 与 SQL Server 之间可直接传输数据。
- 本地资源可通过站点到站点 VPN（VPN 网关）或 ExpressRoute 使用专用 IP 地址访问虚拟网络中的资源。
- 虚拟网络可使用专用 IP 地址进行对等互连，实现虚拟网络中资源之间的彼此通信。
- 虚拟网络中的服务实例通常由 Azure 服务完全托管。 这包括监视资源的运行状况并根据负载进行缩放。
- 服务实例部署在虚拟网络的子网中。 根据服务提供的指南，必须通过网络安全组对子网开放入站和出站网络访问。
- 某些服务还会对它们能够部署到其中的子网施加限制，限制策略、路由的应用，或者要求将 VM 和服务资源组合到同一子网中。 请查看每项服务，了解这些具体限制，因为它们会随时间而变化。 应用服务是此类服务的一个示例。
    
    <!--Not Available on  Azure NetApp Files, Dedicated HSM, Azure Container Instances-->
    
- （可选）服务可能需要一个委派子网作为显式标识符，用于表示子网可承载特定服务。 服务可以通过委托获得显式权限，可以在委托的子网中创建服务专属资源。
- 如需 REST API 响应的示例，请参阅包含委托子网的虚拟网络。 可以通过可用委托 API 获得一个内容广泛的列表，其中包含的服务使用委托子网模型。

有关可部署到虚拟网络中的服务的列表，请参阅[将专用 Azure 服务部署到虚拟网络](virtual-network-for-azure-services.md)。

## <a name="private-link-and-private-endpoints"></a>专用链接和专用终结点

使用专用终结点，可以允许事件通过专用链接安全地从虚拟网络直接进入 Azure 资源，而无需通过公共 Internet。 专用终结点是虚拟网络中的 Azure 服务的特殊网络接口。 为 Azure 资源创建专用终结点时，它会在虚拟网络上的客户端与你的 Azure 资源之间提供安全连接。 从虚拟网络的 IP 地址范围为专用终结点分配 IP 地址。 专用终结点与 Azure 服务之间的连接使用安全的专用链接。

下面的示例展示了事件网格资源专用终结点的私密访问，该终结点在虚拟网络上的客户端与事件网格资源之间提供安全连接。

:::image type="content" source="./media/network-isolation/architecture-diagram.png" alt-text="使用专用终结点进行的 SQL DB 资源私密访问":::

<!--Not Available on [What is Private Link?](../private-link/private-link-overview.md)-->

## <a name="service-endpoints"></a>服务终结点
VNet 服务终结点通过 Azure 主干网络的优化路由提供与 Azure 服务的安全的直接连接。 使用终结点可以保护关键的 Azure 服务资源，只允许在客户自己的虚拟网络中对其进行访问。 服务终结点使 VNet 中的专用 IP 地址能够到达 Azure 服务的终结点，且无需在 VNet 中使用公共 IP 地址。

![在虚拟网络中保护 Azure 服务](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

有关详细信息，请参阅[虚拟网络服务终结点](virtual-network-service-endpoints-overview.md)

## <a name="service-tags"></a>服务标记

服务标记代表给定 Azure 服务中的一组 IP 地址前缀。 使用服务标记，可以在[网络安全组](https://docs.azure.cn/virtual-network/security-overview#security-rules)或 [Azure 防火墙](https://docs.azure.cn/firewall/service-tags)中定义网络访问控制。 通过在规则的相应源字段或目标字段中指定服务标记名（例如，AzureEventGrid），可以允许或拒绝相应服务的流量。

:::image type="content" source="./media/network-isolation/service-tags.png" alt-text="使用服务标记允许或拒绝流量":::

可使用服务标记来实现网络隔离，保护 Azure 资源免受常规 Internet 侵害，同时访问具有公共终结点的 Azure 服务。 可创建入站/出站网络安全组规则，以拒绝进出 Internet 的流量并允许进出 AzureCloud 或特定 Azure 服务的其他[可用服务标记](service-tags-overview.md#available-service-tags)的流量 。

有关服务标记和支持它们的 Azure 服务的详细信息，请参阅[服务标记概述](service-tags-overview.md)

## <a name="next-steps"></a>后续步骤

- 了解如何[将应用与 Azure 网络集成](../app-service/web-sites-integrate-with-vnet.md)。
- 了解如何[使用服务标记限制对资源的访问](tutorial-restrict-network-access-to-resources.md)。

<!--Not Available on [connect privately to an Azure Cosmos account using Azure Private Link](../private-link/create-private-endpoint-cosmosdb-portal.md)-->

<!-- Update_Description: new article about vnet integration for azure services -->
<!--NEW.date: 01/18/2021-->