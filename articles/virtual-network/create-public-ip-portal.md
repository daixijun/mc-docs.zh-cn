---
title: 创建公共 IP - Azure 门户
description: 了解如何在 Azure 门户中创建公共 IP
services: virtual-network
documentationcenter: na
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 08/28/2020
author: rockboyfor
ms.date: 01/18/2021
ms.testscope: yes
ms.testdate: 10/05/2020
ms.author: v-yeche
ms.openlocfilehash: 658f48522b78a6af288cb1f5300b445fd976a10d
ms.sourcegitcommit: c8ec440978b4acdf1dd5b7fda30866872069e005
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98230281"
---
<!--Verified Successfully-->
<!--Remove the part of Availability Zones-->
# <a name="quickstart-create-a-public-ip-address-using-the-azure-portal"></a>快速入门：使用 Azure 门户创建公共 IP 地址

本文介绍了如何使用 Azure 门户来创建公共 IP 地址资源。 若要详细了解这可能关联到哪些资源，以及基本 SKU 和标准 SKU 之间的差异和其他相关信息，请参阅[公共 IP 地址](https://docs.azure.cn/virtual-network/public-ip-addresses)。  对于此示例，我们只重点介绍 IPv4 地址；有关 IPv6 地址的详细信息，请参阅[适用于 Azure VNet 的 IPv6](https://docs.azure.cn/virtual-network/ipv6-overview)。

<!--Not Available on Availability Zones-->


<a name="option-create-public-ip-basic"></a>

# <a name="basic-sku"></a>[**基本 SKU**](#tab/option-create-public-ip-basic)

使用以下步骤创建名为“myBasicPublicIP”的基本静态公共 IP 地址。  基本公共 IP 没有可用性区域的概念。

1. 登录到 [Azure 门户](https://portal.azure.cn/)。
2. 选择“创建资源”。 
3. 在搜索框中键入“公共 IP 地址”。
4. 在搜索结果中时，选择“公共 IP 地址”。 接下来，在“公共 IP 地址”页中，选择“创建”。
5. 在“创建公共 IP 地址”页上，输入或选择以下信息： 

    | 设置                 | 值                       |
    | --- | --- |
    | IP 版本              | 选择“IPv4”                 |    
    | SKU                     | 选择“标准” |
    | 名称                    | 输入“myBasicPublicIP” |
    | IP 地址分配   | 选择“静态”（请参阅下面的注释）                                     |
    | 空闲超时(分钟)  | 将该值保留为“4”        |
    | DNS 名称标签          | 将该值保留为空白    |
    | 订阅            | 选择订阅。   |
    | 资源组          | 选择“新建”，输入“myResourceGroup”，然后选择“确定”  |
    | 位置                | 选择“中国东部 2” |

如果可以接受 IP 地址随时间的推移发生更改，可以选择动态 IP 分配。

---

## <a name="additional-information"></a>其他信息 

若要详细了解上面列出的各个字段，请参阅[管理公共 IP 地址](https://docs.azure.cn/virtual-network/virtual-network-public-ip-address#create-a-public-ip-address)。

## <a name="next-steps"></a>后续步骤
- [将公共 IP 地址关联到虚拟机](https://docs.azure.cn/virtual-network/associate-public-ip-address-vm#azure-portal)
- 详细了解 Azure 中的[公共 IP 地址](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)。
- 详细了解所有[公共 IP 地址设置](virtual-network-public-ip-address.md#create-a-public-ip-address)。

<!-- Update_Description: update meta properties, wording update, update link -->