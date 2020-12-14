---
title: 升级 Azure Kubernetes 服务 (AKS) 群集
description: 了解如何升级 Azure Kubernetes 服务 (AKS) 群集以获取最新的功能和安全更新。
services: container-service
ms.topic: article
origin.date: 11/17/2020
author: rockboyfor
ms.date: 12/14/2020
ms.testscope: no
ms.testdate: 05/25/2020
ms.author: v-yeche
ms.openlocfilehash: f9eec45fc7b4722ec515230bd06bd1043175d1ce
ms.sourcegitcommit: 8f438bc90075645d175d6a7f43765b20287b503b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97004131"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>升级 Azure Kubernetes 服务 (AKS) 群集

在 AKS 群集的生命周期中，经常需要升级到最新的 Kubernetes 版本。 必须应用最新的 Kubernetes 安全版本，或者通过升级来获取最新功能。 本文演示如何在 AKS 群集中升级主组件或单个默认的节点池。

对于使用多个节点池或 Windows Server 节点的 AKS 群集，请参阅[升级 AKS 中的节点池][nodepool-upgrade]。

## <a name="before-you-begin"></a>准备阶段

本文要求运行 Azure CLI 2.0.65 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

> [!WARNING]
> AKS 群集升级会触发节点的隔离和排空。 如果可用计算配额较低，则升级可能会失败。 有关详细信息，请参阅[增加配额](https://support.azure.cn/support/support-azure/)。

## <a name="check-for-available-aks-cluster-upgrades"></a>检查是否有可用的 AKS 群集升级

若要检查哪些 Kubernetes 版本可用于群集，请使用 [az aks get-upgrades][az-aks-get-upgrades] 命令。 以下示例在名为 *myResourceGroup* 的资源组中检查是否有可供名为 *myAKSCluster* 的群集使用的升级：

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> 升级受支持的 AKS 群集时，不能跳过 Kubernetes 次要版本。 例如，允许从 1.12.x 升级到 1.13.x，或者从 1.13.x 升级到 1.14.x，但不允许从 1.12.x 升级到 1.14.x。
>
> 若要从 1.12.x 升级到 1.14.x，请先从 1.12.x 升级到 1.13.x，然后再从 1.13.x 升级到 1.14.x。
>
> 仅当从不受支持的版本升级回受支持的版本时，才可以跳过多个版本。 例如，可以从不受支持的 1.10.x 升级到受支持的 1.15.x 。

以下示例输出表明，群集可以升级到版本 1.13.9 和 1.13.10：

```console
Name     ResourceGroup     MasterVersion    NodePoolVersion    Upgrades
-------  ----------------  ---------------  -----------------  ---------------
default  myResourceGroup   1.12.8           1.12.8             1.13.9, 1.13.10
```

如果没有可用的升级，你将获得：
```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

<!--Not Available on ## Customize node surge upgrade (Preview) till 08/05/2020-->
<!--Not Available on ### Set up the preview feature for customizing node surge upgrade-->
<!--Not Available on feature `MaxSurgePreview` az feature register --namespace "Microsoft.ContainerService" --name "MaxSurgePreview"-->

## <a name="upgrade-an-aks-cluster"></a>升级 AKS 群集

如果有一系列适用于 AKS 群集的版本，则可使用 [az aks upgrade][az-aks-upgrade] 命令进行升级。 在升级过程中，AKS 会将一个新的缓冲区节点添加到运行指定 Kubernetes 版本的群集。 然后，它会[隔离并排空][kubernetes-drain]旧节点之一，以最大程度地减少对正在运行的应用程序的干扰（如果你使用的是最大浪涌，它会同时[隔离并排空][kubernetes-drain]与指定的缓冲区节点数相同的节点数）。 旧节点在完全排空时，会被重置映像以接收新版本，并且会成为下一个要升级的节点的缓冲区节点。 此过程会重复进行，直至群集中的所有节点都已升级完毕。 在此过程结束时，将删除上一个排空节点，从而维持现有的代理节点计数。

<!--Not Available on [max surge](#customize-node-surge-upgrade)-->

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

升级群集需要几分钟时间，具体取决于有多少节点。

> [!NOTE]
> 允许群集升级完成的总时间。 此时间是通过取 `10 minutes * total number of nodes in the cluster` 的乘积来计算的。 例如，在 20 节点群集中，升级操作必须在 200 分钟内成功，否则 AKS 将使操作失败，以避免出现无法恢复的群集状态。 若要在升级失败时恢复，请在达到超时值后重试升级操作。

若要确认升级是否成功，请使用 [az aks show][az-aks-show] 命令：

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

以下示例输出表明群集现在运行 1.13.10：

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  chinaeast2      myResourceGroup  1.13.10               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.chinaeast2.cx.prod.service.azk8s.cn
```

## <a name="next-steps"></a>后续步骤

本文演示了如何升级现有的 AKS 群集。 若要详细了解如何部署和管理 AKS 群集，请参阅相关教程系列。

> [!div class="nextstepaction"]
> [AKS 教程][aks-tutorial-prepare-app]

<!-- LINKS - external -->

[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->

[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: https://docs.azure.cn/cli/install-azure-cli
[az-aks-get-upgrades]: https://docs.azure.cn/cli/aks#az_aks_get_upgrades
[az-aks-upgrade]: https://docs.azure.cn/cli/aks#az_aks_upgrade
[az-aks-show]: https://docs.azure.cn/cli/aks#az_aks_show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[az-extension-add]: https://docs.azure.cn/cli/extension#az_extension_add
[az-extension-update]: https://docs.azure.cn/cli/extension#az_extension_update

<!-- Update_Description: update meta properties, wording update, update link -->