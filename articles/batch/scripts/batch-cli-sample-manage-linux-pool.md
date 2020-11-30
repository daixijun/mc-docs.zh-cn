---
title: Azure CLI 脚本示例 - Batch 中的 Linux 池
description: 此脚本演示了 Azure CLI 中一些可用于在 Azure Batch 中创建和管理 Linux 计算节点池的命令。
ms.topic: sample
origin.date: 01/29/2018
author: rockboyfor
ms.date: 11/23/2020
ms.testscope: no
ms.testdate: 08/24/2020
ms.author: v-yeche
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1f6c35e1fb5eb78a9e04c72700bf9e87779dc32d
ms.sourcegitcommit: c2c9dc65b886542d220ae17afcb1d1ab0a941932
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "95970717"
---
# <a name="cli-example-create-and-manage-a-linux-pool-in-azure-batch"></a>CLI 示例：在 Azure Batch 中创建和管理 Linux 池

此脚本演示了 Azure CLI 中一些可用于在 Azure Batch 中创建和管理 Linux 计算节点池的命令。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 本教程需要 Azure CLI 版本 2.0.20 或更高版本。

<!--Not Available on Azure Cloud Shell-->

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)]

## <a name="example-script"></a>示例脚本

```azurecli
#!/bin/bash

# Create a resource group.
az group create --name myResourceGroup --location chinanorth

# Create a Batch account.
az batch account create \
    --resource-group myResourceGroup \
    --name mybatchaccount \
    --location chinanorth

# Authenticate Batch account CLI session.
az batch account login \
    --resource-group myResourceGroup \
    --name mybatchaccount
    --shared-key-auth

# Retrieve a list of available images and node agent SKUs.
az batch pool node-agent-skus list

# Create a new Linux pool with a virtual machine configuration. The image reference 
# and node agent SKUs ID can be selected from the ouptputs of the above list command.
# The image reference is in the format: {publisher}:{offer}:{sku}:{version} where {version} is
# optional and defaults to 'latest'.
az batch pool create \
    --id mypool-linux \
    --vm-size Standard_A1 \
    --image canonical:ubuntuserver:16.04-LTS \
    --node-agent-sku-id "batch.node.ubuntu 16.04"

# Resize the pool to start some VMs.
az batch pool resize \
    --pool-id mypool-linux \
    --target-dedicated 5

# Check the status of the pool to see when it has finished resizing.
az batch pool show \
    --pool-id mypool-linux

# List the compute nodes running in a pool.
az batch node list \
    --pool-id mypool-linux

# If a particular node in the pool is having issues, it can be rebooted or reimaged.
# The ID of the node can be retrieved with the list command above.
# A typical node ID is in the format 'tvm-xxxxxxxxxx_1-<timestamp>'.
az batch node reboot \
    --pool-id mypool-linux \
    --node-id tvm-123_1-20170316t000000z

# One or more compute nodes can be deleted from the pool, and any
# work already assigned to it can be re-allocated to another node.
az batch node delete \
    --pool-id mypool-linux \
    --node-list tvm-123_1-20170316t000000z tvm-123_2-20170316t000000z \
    --node-deallocation-option requeue

```

## <a name="clean-up-deployment"></a>清理部署

运行以下命令以删除资源组及其相关的所有资源。

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [az group create](https://docs.azure.cn/cli/group#az_group_create) | 创建用于存储所有资源的资源组。 |
| [az batch account create](https://docs.azure.cn/cli/batch/account#az_batch_account_create) | 创建批处理帐户。 |
| [az batch account login](https://docs.azure.cn/cli/batch/account#az_batch_account_login) | 针对指定的批处理帐户进行身份验证，以便进一步进行 CLI 交互。  |
| [az batch pool node-agent-skus list](../batch-linux-nodes.md#list-of-virtual-machine-images) | 列出可用节点代理 SKU 和映像信息。  |
| [az batch pool create](https://docs.azure.cn/cli/batch/pool#az_batch_pool_create) | 创建计算节点池。  |
| [az batch pool resize](https://docs.azure.cn/cli/batch/pool#az_batch_pool_resize) | 调整指定池中正在运行的 VM 数目。  |
| [az batch pool show](https://docs.azure.cn/cli/batch/pool#az_batch_pool_show) | 显示池的属性。  |
| [az batch node list](https://docs.azure.cn/cli/batch/node#az_batch_node_list) | 列出指定池中的所有计算节点。  |
| [az batch node reboot](https://docs.azure.cn/cli/batch/node#az_batch_node_reboot) | 重新启动指定的计算节点。  |
| [az batch node delete](https://docs.azure.cn/cli/batch/node#az_batch_node_delete) | 从指定的池中删除列出的节点。  |
| [az group delete](https://docs.azure.cn/cli/group#az_group_delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.azure.cn/cli)。

<!-- Update_Description: update meta properties, wording update, update link -->