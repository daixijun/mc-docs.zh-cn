---
title: 创建公共 IP - Azure CLI
description: 了解如何使用 Azure CLI 创建公共 IP
services: virtual-network
documentationcenter: na
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 08/28/2020
author: rockboyfor
ms.date: 11/30/2020
ms.testscope: yes
ms.testdate: 10/05/2020
ms.author: v-yeche
ms.openlocfilehash: 7693e2bb319ad65cd46109c2135ad81f2b31c802
ms.sourcegitcommit: b6fead1466f486289333952e6fa0c6f9c82a804a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2020
ms.locfileid: "96300807"
---
<!--Verified Successfully-->
<!--Remove the part of Availability Zones-->
# <a name="quickstart-create-a-public-ip-address-using-azure-cli"></a>快速入门：使用 Azure CLI 创建公共 IP 地址

本文介绍了如何使用 Azure CLI 来创建公共 IP 地址。 若要详细了解这可能关联到哪些资源，以及基本 SKU 和标准 SKU 之间的差异和其他相关信息，请参阅[公共 IP 地址](https://docs.azure.cn/virtual-network/public-ip-addresses)。  对于此示例，我们只重点介绍 IPv4 地址；有关 IPv6 地址的详细信息，请参阅[适用于 Azure VNet 的 IPv6](https://docs.azure.cn/virtual-network/ipv6-overview)。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 Azure CLI 2.0.28 或更高版本。 

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)] 

## <a name="create-a-resource-group"></a>创建资源组

Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

使用 [az group create](https://docs.azure.cn/cli/group#az_group_create) 在 chinaeast2 位置创建名为“myResourceGroup”的资源组 。

```azurecli
  az group create \
    --name myResourceGroup \
    --location chinaeast2
```
---

<!--Not Available on # [**Standard SKU - Using zones**](#tab/option-create-public-ip-standard-zones)-->
<!--Not Available on Availability Zones-->

<!--Not Available on [Availability Zones](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/virtual-network/toc.json#availability-zones)-->

# <a name="standard-sku---no-zones"></a>[标准 SKU - 无区域](#tab/option-create-public-ip-standard)

>[!NOTE]
>以下命令适用于 API 版本 2020-08-01 或更高版本。  有关当前正在使用的 API 版本的详细信息，请参阅[资源提供程序和类型](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types)。

使用 [az network public-ip create](https://docs.azure.cn/cli/network/public-ip#az_network_public_ip_create) 在 myResourceGroup 中创建一个标准公共 IP 地址作为名为 myStandardPublicIP 的非区域性资源 。

```azurecli
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardPublicIP \
    --sku Standard
```
此选择在所有区域均有效，并且在没有[可用性区域](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/virtual-network/toc.json#availability-zones)的区域中，这是标准公共 IP 地址的默认选择。

# <a name="basic-sku"></a>[**基本 SKU**](#tab/option-create-public-ip-basic)

使用 [az network public-ip create](https://docs.azure.cn/cli/network/public-ip#az_network_public_ip_create) 在 myResourceGroup 中创建名为“myBasicPublicIP”的基本静态公共 IP 地址 。  基本公共 IP 没有可用性区域的概念。

```azurecli
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myBasicPublicIP \
    --sku Basic \
    --allocation-method Static
```

如果可以接受 IP 地址随时间的推移发生更改，可以通过将 allocation-method 更改为“Dynamic”来选择动态 IP 分配。

---

## <a name="additional-information"></a>其他信息 

若要更详细地了解以上所列各个变量，请参阅[管理公共 IP 地址](https://docs.azure.cn/virtual-network/virtual-network-public-ip-address#create-a-public-ip-address)。

## <a name="next-steps"></a>后续步骤
- [将公共 IP 地址关联到虚拟机](https://docs.azure.cn/virtual-network/associate-public-ip-address-vm#azure-portal)。
- 详细了解 Azure 中的[公共 IP 地址](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)。
- 详细了解所有[公共 IP 地址设置](virtual-network-public-ip-address.md#create-a-public-ip-address)。

<!-- Update_Description: update meta properties, wording update, update link -->