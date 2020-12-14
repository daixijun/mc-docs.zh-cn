---
title: 升级 Azure Kubernetes 服务 (AKS) 节点映像
description: 了解如何升级 AKS 群集节点和节点池上的映像。
ms.service: container-service
ms.topic: conceptual
origin.date: 11/17/2020
author: rockboyfor
ms.date: 12/14/2020
ms.testscope: yes
ms.testdate: 09/14/2020
ms.author: v-yeche
ms.openlocfilehash: 6120e1531257e5901105e824efce10d3125e5037
ms.sourcegitcommit: 8f438bc90075645d175d6a7f43765b20287b503b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97004095"
---
<!--Verified successfully-->
# <a name="azure-kubernetes-service-aks-node-image-upgrade"></a>Azure Kubernetes 服务 (AKS) 节点映像升级

AKS 支持升级节点上的映像，以便你能够获取最新的操作系统和运行时更新。 AKS 每周提供一个带有最新更新的新映像，因此，建议定期升级节点的映像以使用最新功能，包括 Linux 或 Windows 补丁。 本文介绍了在不升级 Kubernetes 版本的情况下如何升级 AKS 群集节点映像以及如何更新节点池映像。

如果需要了解 AKS 提供的最新映像，请参阅 [AKS 发行说明](https://github.com/Azure/AKS/releases)以获取更多详细信息。

有关如何升级群集的 Kubernetes 版本的信息，请参阅[升级 AKS 群集][upgrade-cluster]。

## <a name="limitations"></a>限制

* AKS 群集必须对节点使用虚拟机规模集。

## <a name="install-the-aks-cli-extension"></a>安装 AKS CLI 扩展

在下一个核心 CLI 版本发布之前，你需要具备 aks-preview CLI 扩展才能使用节点映像升级。 使用 [az extension add][az-extension-add] 命令，然后使用 [az extension update][az-extension-update] 命令来查找任何可用的更新：

```azurecli
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="upgrade-all-nodes-in-all-node-pools"></a>升级所有节点池中的所有节点

升级节点映像是使用 `az aks upgrade` 来完成的。 若要升级节点映像，请使用以下命令：

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-image-only
```

在升级过程中，请使用下面的 `kubectl` 命令来检查节点映像的状态，以获取标签并筛选出当前节点映像信息：

<!--MOONCAKE: CORRECT ON {range .items[*]} THERE ARE ONE BLANK AFTER RANGE KEY WORDS-->

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

升级完成后，请使用 `az aks show` 来获取更新后的节点池详细信息。 当前节点映像在 `nodeImageVersion` 属性中显示。

```azurecli
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster
```

## <a name="upgrade-a-specific-node-pool"></a>升级特定节点池

升级节点池上的映像类似于升级群集上的映像。

若要在不执行 Kubernetes 群集升级的情况下更新节点池的操作系统映像，请使用以下示例中的 `--node-image-only` 选项：

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-image-only
```

在升级过程中，请使用下面的 `kubectl` 命令来检查节点映像的状态，以获取标签并筛选出当前节点映像信息：

<!--MOONCAKE: CORRECT ON {range .items[*]} THERE ARE ONE BLANK AFTER RANGE KEY WORDS-->

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

升级完成后，请使用 `az aks nodepool show` 来获取更新后的节点池详细信息。 当前节点映像在 `nodeImageVersion` 属性中显示。

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

<!--Not Available on ## Upgrade node images with node surge-->
<!--MOONCAKE: NOT SUPPORT ON --max-surge PARAMETER-->

## <a name="next-steps"></a>后续步骤

- 参阅 [AKS 发行说明](https://github.com/Azure/AKS/releases)以了解有关最新节点映像的信息。
- 通过阅读[升级 AKS 群集][upgrade-cluster]一文来了解如何升级 Kubernetes 版本。
- [将安全更新和内核更新应用于 Azure Kubernetes 服务 (AKS) 中的 Linux 节点][security-update]
- 通过阅读[创建和管理多个节点池][use-multiple-node-pools]一文来详细了解多个节点池以及如何升级节点池。

<!-- LINKS - internal -->

[upgrade-cluster]: upgrade-cluster.md
[security-update]: node-updates-kured.md
[use-multiple-node-pools]: use-multiple-node-pools.md

<!--Not Available on [max-surge]: upgrade-cluster.md#customize-node-surge-upgrade-->

[az-extension-add]: https://docs.azure.cn/cli/extension#az_extension_add
[az-extension-update]: https://docs.azure.cn/cli/extension#az_extension_update

<!-- Update_Description: update meta properties, wording update, update link -->