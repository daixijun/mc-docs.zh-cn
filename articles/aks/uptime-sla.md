---
title: Azure Kubernetes 服务 (AKS) 与运行时间 SLA
description: 了解 Azure Kubernetes 服务 (AKS) API 服务器的可选运行时间 SLA 产品/服务。
services: container-service
ms.topic: conceptual
origin.date: 06/24/2020
author: rockboyfor
ms.date: 11/23/2020
ms.testscope: yes
ms.testdate: 11/20/2020
ms.author: v-yeche
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 486fe49e44d441c6c0ea8a35f2e7ab6b1b65d6e2
ms.sourcegitcommit: c2c9dc65b886542d220ae17afcb1d1ab0a941932
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94978333"
---
<!--Verified successfully-->
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>Azure Kubernetes 服务 (AKS) 运行时间 SLA

运行时间 SLA 是一个可选功能，用于为群集启用具有财务支持的更高 SLA。 对于不使用可用性区域的群集，运行时间 SLA 可保证 Kubernetes API 服务器终结点 99.9% 的可用性。 AKS 跨更新域和容错域使用主节点副本，以确保满足 SLA 要求。

<!--Not Available on [Availability Zones][availability-zones]-->

客户如果需要 SLA 以满足合规性要求或需要将 SLA 扩展到其最终用户，则应启用此功能。 具有将从更高的运行时间 SLA 中受益的关键工作负载的客户也可能受益。 将运行时间 SLA 功能与可用性区域结合使用可以提高 Kubernetes API 服务器运行时间的可用性。  

客户仍可以创建服务级别目标 (SLO) 为 99.5% 的无限制免费群集，并根据需要选择首选 SLO 或 SLA 运行时间。

> [!Important]
> 对于出口锁定的群集，请参阅[限制出口流量](limit-egress-traffic.md)以打开适当的端口。

## <a name="region-availability"></a>上市区域

运行时间 SLA 在[支持 AKS](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service&regions=,china-non-regional,china-east,china-east-2,china-north,china-north-2,) 的 Azure 中国云区域提供。

## <a name="limitations"></a>限制

* 当前不支持专用群集。

## <a name="sla-terms-and-conditions"></a>SLA 条款和条件

运行时间 SLA 是付费功能，按群集启用。 运行时间 SLA 定价取决于离散群集的数量，而不是各个群集的大小。 有关详细信息，可以查看[运行时间 SLA 定价详细信息](https://www.azure.cn/pricing/details/kubernetes-service/)。

## <a name="before-you-begin"></a>开始之前

* 安装 [Azure CLI](https://docs.azure.cn/cli/install-azure-cli) 版本 2.8.0 或更高版本

## <a name="creating-a-new-cluster-with-uptime-sla"></a>创建具有运行时间 SLA 的新群集

> [!NOTE]
> 目前，如果启用运行时间 SLA，则无法将其从群集中删除。

若要创建具有运行时间 SLA 的群集，请使用 Azure CLI。

以下示例在“chinaeast2”位置创建名为“myResourceGroup”的资源组：

```azurecli
# Create a resource group
az group create --name myResourceGroup --location chinaeast2
```
使用 [`az aks create`][az-aks-create] 命令创建 AKS 群集。 以下示例创建一个具有一个节点的名为 myAKSCluster 的群集。 此操作需要几分钟才能完成：

```azurecli
# Create an AKS cluster with uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1
```
片刻之后，该命令将会完成，并返回有关群集的 JSON 格式信息。 下面的 JSON 片段显示了 SKU 的付费层，表明群集已启用了运行时间 SLA：

```output
},
"sku": {
"name": "Basic",
"tier": "Paid"
},
```

## <a name="modify-an-existing-cluster-to-use-uptime-sla"></a>修改现有群集以使用运行时间 SLA

可以选择更新现有群集，以使用运行时间 SLA。

如果使用前面的步骤创建了 AKS 群集，请删除资源组：

```azurecli
# Delete the existing cluster by deleting the resource group 
az group delete --name myResourceGroup --yes --no-wait
```

创建新的资源组：

```azurecli
# Create a resource group
az group create --name myResourceGroup --location chinaeast2
```

创建新群集，不使用运行时间 SLA：

```azurecli
# Create a new cluster without uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 1
```

使用 [`az aks update`][az-aks-nodepool-update] 命令更新现有群集：

```azurecli
# Update an existing cluster to use Uptime SLA
az aks update --resource-group myResourceGroup --name myAKSCluster --uptime-sla
```

下面的 JSON 片段显示了 SKU 的付费层，表明群集已启用了运行时间 SLA：

```output
},
  "sku": {
    "name": "Basic",
    "tier": "Paid"
},
```

## <a name="clean-up"></a>清理

若要避免收费，请清理创建的任何资源。 若要删除群集，请使用 [`az group delete`][az-group-delete] 命令删除 AKS 资源组：

```azurecli
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>后续步骤

<!--Not Available on Use [Availability Zones][availability-zones]-->

将群集配置为[限制出口流量](limit-egress-traffic.md)。

<!-- LINKS - External -->

[azure-support]: https://support.azure.cn/support/support-azure/
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service&regions=china-non-regional,china-east,china-east-2,china-north,china-north-2

<!-- LINKS - Internal -->

[vm-skus]: ../virtual-machines/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[faq]: ./faq.md
    
<!--Not Available on [availability-zones]: ./availability-zones.md-->

[az-aks-create]: https://docs.azure.cn/cli/aks#az_aks_create
[limit-egress-traffic]: ./limit-egress-traffic.md
[az-extension-add]: https://docs.azure.cn/cli/extension#az_extension_add
[az-extension-update]: https://docs.azure.cn/cli/extension#az_extension_update
[az-aks-nodepool-update]: https://docs.azure.cn/cli/aks/nodepool#az_aks_nodepool_update
[az-group-delete]: https://docs.azure.cn/cli/group#az_group_delete

<!-- Update_Description: new article about uptime sla -->
<!--NEW.date: 11/23/2020-->
