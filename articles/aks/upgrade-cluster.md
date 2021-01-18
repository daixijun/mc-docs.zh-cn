---
title: 升级 Azure Kubernetes 服务 (AKS) 群集
description: 了解如何升级 Azure Kubernetes 服务 (AKS) 群集以获取最新的功能和安全更新。
services: container-service
ms.topic: article
origin.date: 12/17/2020
author: rockboyfor
ms.date: 01/11/2021
ms.testscope: no
ms.testdate: 05/25/2020
ms.author: v-yeche
ms.openlocfilehash: db85a56205308eca56b9096719e4539d048ada54
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98022881"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>升级 Azure Kubernetes 服务 (AKS) 群集

AKS 群集生命周期的一部分涉及到定期升级到最新的 Kubernetes 版本。 必须应用最新的安全版本，或者通过升级来获取最新功能。 本文演示如何在 AKS 群集中升级主组件或单个默认的节点池。

对于使用多个节点池或 Windows Server 节点的 AKS 群集，请参阅[升级 AKS 中的节点池][nodepool-upgrade]。

## <a name="before-you-begin"></a>准备阶段

本文要求运行 Azure CLI 2.0.65 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

> [!WARNING]
> AKS 群集升级会触发节点的隔离和排空。 如果可用计算配额较低，则升级可能会失败。 有关详细信息，请参阅[增加配额](https://support.azure.cn/support/support-azure/)

## <a name="check-for-available-aks-cluster-upgrades"></a>检查是否有可用的 AKS 群集升级

若要检查哪些 Kubernetes 版本可用于群集，请使用 [az aks get-upgrades][az-aks-get-upgrades] 命令。 下面的示例会检查 myResourceGroup 中到 myAKSCluster 的可用升级 ：

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> 升级受支持的 AKS 群集时，不能跳过 Kubernetes 次要版本。 所有升级都必须按主版本号依次执行。 例如，允许 1.14.x -> 1.15.x 或 1.15.x -> 1.16.x 之间进行升级，但不允许 1.14.x -> 1.16.x 之间的升级     。 
> > 仅当从不受支持的版本升级回受支持的版本时，才可以跳过多个版本 。 例如，可从不受支持的 1.10.x 升级到受支持的 1.15.x 。

以下示例输出表明，群集可升级到版本 1.19.1 和 1.19.3 ：

```console
Name     ResourceGroup    MasterVersion    Upgrades
------- ---------------  --------------- --------------
default  myResourceGroup  1.18.10          1.19.1, 1.19.3
```

如果没有可用的升级，你将收到消息：

```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

<!--Not Available on ## Customize node surge upgrade (Preview) till 01/05/2021-->
<!--Not Available on ### Set up the preview feature for customizing node surge upgrade-->
<!--Not Available on feature `MaxSurgePreview` az feature register --namespace "Microsoft.ContainerService" --name "MaxSurgePreview"-->

## <a name="upgrade-an-aks-cluster"></a>升级 AKS 群集

如果有一系列适用于 AKS 群集的版本，则可使用 [az aks upgrade][az-aks-upgrade] 命令进行升级。 在升级过程中，AKS 将： 
- 将一个新的缓冲区节点添加到运行指定的 Kubernetes 版本的群集。 
    
    <!--Not Available on [max surge](#customize-node-surge-upgrade)-->
    
- [隔离并排空][kubernetes-drain]其中一个旧节点，以最大程度地减少对正在运行的应用程序的干扰（如果你使用的是最大浪涌，它会同时[隔离并排空][kubernetes-drain]与指定的缓冲区节点数相同的节点数）。 
- 旧节点在完全排空时，会被重置映像以接收新版本，并且会成为下一个要升级的节点的缓冲区节点。 
- 此过程会重复进行，直至群集中的所有节点都已升级完毕。 
- 在此过程结束时，将删除上一个缓冲区节点，从而维持现有的代理节点计数和区域均衡。

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

升级群集需要几分钟时间，具体取决于有多少节点。

> [!IMPORTANT]
> 确保任何 `PodDisruptionBudgets` (PDB) 都允许一次至少移动 1 个 Pod 副本，否则排空/逐出操作将失败。
> 如果排空操作失败，则根据设计，升级操作将失败，以确保应用程序不会中断。 请消除导致操作停止的原因（PDB 错误、缺少配额等），然后重试该操作。

若要确认升级是否成功，请使用 [az aks show][az-aks-show] 命令：

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

以下示例输出表明群集现在运行 1.13.10：

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------ ----------  --------------- -------------------  ------------------- ----------------------------------------------
myAKSCluster  chinaeast2      myResourceGroup  1.18.10              Succeeded            myakscluster-dns-379cbbb9.hcp.chinaeast2.cx.prod.service.azk8s.cn
```

<!--Not Available on ## Set auto-upgrade channel-->
<!--Not Available on az feature register --namespace Microsoft.ContainerService -n AutoUpgradePreview-->


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
[az-extension-add]: https://docs.azure.cn/cli/extension#az_extension_add
[az-extension-update]: https://docs.azure.cn/cli/extension#az_extension_update
[az-feature-list]: https://docs.azure.cn/cli/feature#az_feature_list
[az-feature-register]: https://docs.azure.cn/cli/feature#az_feature_register
[az-provider-register]: https://docs.azure.cn/cli/provider#az_provider_register
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[upgrade-cluster]:  #upgrade-an-aks-cluster

<!-- Update_Description: update meta properties, wording update, update link -->