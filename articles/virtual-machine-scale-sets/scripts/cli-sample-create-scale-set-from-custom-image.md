---
title: Azure CLI 示例 - 使用自定义 VM 映像
description: 此脚本创建使用自定义 VM 映像作为 VM 实例源的虚拟机规模集。
author: axayjo
ms.author: v-junlch
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.date: 08/07/2020
ms.reviewer: cynthn
ms.custom: akjosh, devx-track-azurecli
ms.openlocfilehash: bf198778bb633fdd2a82a7a10b14d172114e31f9
ms.sourcegitcommit: f436acd1e2a0108918a6d2ee9a1aac88827d6e37
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509035"
---
# <a name="create-a-virtual-machine-scale-set-from-a-custom-vm-image-with-the-azure-cli"></a>使用 Azure CLI 基于自定义 VM 映像创建虚拟机规模集
此脚本创建使用自定义 VM 映像作为 VM 实例源的虚拟机规模集。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

## <a name="sample-script"></a>示例脚本
```azurecli
#!/bin/bash

# Create a resource group
az group create --name myResourceGroup --location chinanorth

# Create a scale set

# Custom VM image must already exist in your subscription
# See http://docs.azure.cn/virtual-machine-scale-sets/tutorial-use-custom-image-cli

# Network resources such as an Azure load balancer are automatically created
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image myImage \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="clean-up-deployment"></a>清理部署
运行以下命令可删除资源组、规模集和所有相关资源。

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明
此脚本使用以下命令创建资源组、虚拟机规模集和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](/cli/ad/group) | 创建用于存储所有资源的资源组。 |
| [az vmss create](/cli/vmss) | 创建虚拟机规模集并将其连接到虚拟网络、子网和网络安全组。 负载均衡器也会被创建，以将流量分配到多个 VM 实例。 此命令还指定要使用的 VM 映像和管理凭据。  |
| [az group delete](/cli/ad/group) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤
有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/overview)。

