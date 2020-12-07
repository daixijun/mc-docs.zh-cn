---
title: Azure CLI 示例 - 安装应用
description: 此脚本创建运行 Ubuntu 的虚拟机规模集，并使用自定义脚本扩展安装一个基本 Web 应用程序。
author: mimckitt
ms.author: v-junlch
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: cli
ms.date: 08/07/2020
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 57921773be973b7d9e7536ac6d09942224f62b83
ms.sourcegitcommit: f436acd1e2a0108918a6d2ee9a1aac88827d6e37
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509084"
---
# <a name="install-applications-into-a-virtual-machine-scale-set-with-the-azure-cli"></a>使用 Azure CLI 将应用程序安装到虚拟机规模集中
此脚本创建运行 Ubuntu 的虚拟机规模集，并使用自定义脚本扩展安装一个基本 Web 应用程序。 运行脚本后，可以通过 Web 浏览器访问该 Web 应用。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

## <a name="sample-script"></a>示例脚本
```azurecli
#!/bin/bash

# Create a resource group
az group create --name myResourceGroup --location chinanorth

# Create a scale set
# Network resources such as an Azure load balancer are automatically created
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys

# Install the Azure Custom Script Extension to run an install script
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"],"commandToExecute":"./automate_nginx.sh"}'

# Create a load balancer rule to allow web traffic to reach VM instances
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp

# Show the public IP address of the load balancer and view your web servers
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

## <a name="clean-up-deployment"></a>清理部署
运行以下命令可删除资源组、规模集和所有相关资源。

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明
此脚本使用以下命令创建资源组、虚拟机规模集和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [az group create](/cli/ad/group) | 创建用于存储所有资源的资源组。 |
| [az vmss create](/cli/vmss) | 创建虚拟机规模集并将其连接到虚拟网络、子网和网络安全组。 负载均衡器也会被创建，以将流量分配到多个 VM 实例。 此命令还指定要使用的 VM 映像和管理凭据。  |
| [az vmss extension set](/cli/vmss/extension) | 安装 Azure 自定义脚本扩展以运行脚本，从而在每个 VM 实例上准备数据磁盘。 |
| [az network lb rule create](/cli/network/lb/rule) | 创建负载均衡器规则，以将 TCP 端口 80 上的流量分摊到规模集中的各个 VM 实例上。 |
| [az network public-ip show](https://docs.azure.cn/zh-cn/cli/network/public-ip?view=azure-cli-latest#az-network-public-ip-show) | 获取有关负载均衡器使用的分配公共 IP 地址的信息。 |
| [az group delete](/cli/ad/group) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤
有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/overview)。

