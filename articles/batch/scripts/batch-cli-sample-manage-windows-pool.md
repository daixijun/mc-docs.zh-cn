---
title: Azure CLI 脚本示例 - Batch 中的 Windows 池
description: 此脚本演示了 Azure Batch 中一些可用于在 Azure CLI 中创建和管理 Windows 计算节点池的命令。
ms.topic: sample
origin.date: 12/12/2019
author: rockboyfor
ms.date: 11/23/2020
ms.testscope: no
ms.testdate: ''
ms.author: v-yeche
ms.custom: devx-track-azurecli
ms.openlocfilehash: 12aefdfd9cfbf6f5241c04d723e917f07ab03615
ms.sourcegitcommit: c2c9dc65b886542d220ae17afcb1d1ab0a941932
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "95970715"
---
# <a name="cli-example-create-and-manage-a-windows-pool-in-azure-batch"></a>CLI 示例：在 Azure Batch 中创建和管理 Windows 池

此脚本演示了 Azure Batch 中一些可用于在 Azure CLI 中创建和管理 Windows 计算节点池的命令。 可通过两种方式配置 Windows 池：使用云服务配置，或使用虚拟机配置。 此示例介绍如何使用云服务配置来创建 Windows 池。

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

# Create a new Windows cloud service platform pool with 3 Standard A1 VMs.
# The pool has a start task that runs a basic shell command. Typically a 
# start task copies application files to the pool nodes.
az batch pool create \
    --id mypool-windows \
    --os-family 4 \
    --target-dedicated 3 \
    --vm-size small \
    --start-task-command-line "cmd /c dir /s" \
    --start-task-wait-for-success \
    --application-package-references myapp

# Add some metadata to the pool.
az batch pool set --pool-id mypool-windows --metadata IsWindows=true VMSize=StandardA1

# Change the pool to enable automatic scaling of compute nodes.
# This autoscale formula specifies that the number of nodes should be adjusted according
# to the number of active tasks, up to a maximum of 10 compute nodes.
az batch pool autoscale enable \
    --pool-id mypool-windows \
    --auto-scale-formula "$averageActiveTaskCount = avg($ActiveTasks.GetSample(TimeInterval_Minute * 15));$TargetDedicated = min(10, $averageActiveTaskCount);"

# Monitor the resizing of the pool.
az batch pool show --pool-id mypool-windows

# Disable autoscaling when we no longer require the pool to automatically scale.
az batch pool autoscale disable \
    --pool-id mypool-windows
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
| [az batch account login](https://docs.azure.cn/cli/batch/account#az_batch_account_login) | 针对指定的批处理帐户进行身份验证，以便进一步进行 CLI 交互。 |
| [az batch pool create](https://docs.azure.cn/cli/batch/pool#az_batch_pool_create) | 创建计算节点池。  |
| [az batch pool set](https://docs.azure.cn/cli/batch/pool#az_batch_pool_set) | 更新池的属性。  |
| [az batch pool autoscale enable](https://docs.azure.cn/cli/batch/pool/autoscale#az_batch_pool_autoscale_enable) | 对池启用自动缩放并应用公式。  |
| [az batch pool show](https://docs.azure.cn/cli/batch/pool#az_batch_pool_show) | 显示池的属性。  |
| [az batch pool autoscale disable](https://docs.azure.cn/cli/batch/pool/autoscale#az_batch_pool_autoscale_disable) | 对池禁用自动缩放。 |
| [az group delete](https://docs.azure.cn/cli/group#az_group_delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.azure.cn/cli)。

<!-- Update_Description: update meta properties, wording update, update link -->