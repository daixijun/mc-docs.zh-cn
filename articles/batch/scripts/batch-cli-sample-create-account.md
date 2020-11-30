---
title: Azure CLI 脚本示例 - 创建 Batch 帐户 - Batch 服务
description: 此脚本在 Batch 服务模式下创建 Azure Batch 帐户，并演示如何查询或更新该帐户的各个属性。
ms.topic: sample
origin.date: 01/29/2018
author: rockboyfor
ms.date: 11/23/2020
ms.testscope: no
ms.testdate: ''
ms.author: v-yeche
ms.custom: devx-track-azurecli
ms.openlocfilehash: e64ea70bf1eb912cdc20b0daba7593114ae1aabc
ms.sourcegitcommit: c2c9dc65b886542d220ae17afcb1d1ab0a941932
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "95970719"
---
# <a name="cli-example-create-a-batch-account-in-batch-service-mode"></a>CLI 示例：在 Batch 服务模式下创建 Batch 帐户

此脚本在 Batch 服务模式下创建 Azure Batch 帐户，并演示如何查询或更新该帐户的各个属性。 在默认 Batch 服务模式下创建 Batch 帐户时，其计算节点由 Batch 服务在内部分配。 分配的计算节点受到单独的 vCPU（核心）配额的限制。可以通过共享密钥凭据或 Azure Active Directory 令牌对帐户进行身份验证。

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

# Display the details of the created account.
az batch account show \
    --resource-group myResourceGroup \ 
    --name mybatchaccount

# Add a storage account reference to the Batch account for use as 'auto-storage'
# for applications. Start by creating the storage account.
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --location chinanorth \
    --sku Standard_LRS

# Update the Batch account with the either the name (if they exist in
# the same resource group) or the full resource ID of the storage account.
az batch account set \
    --resource-group myResourceGroup \
    --name mybatchaccount \
    --storage-account mystorageaccount

# View the access keys to the Batch Account for future client authentication.
az batch account keys list \
    --resource-group myResourceGroup \
    --name mybatchaccount

# Authenticate against the account directly for further CLI interaction.
az batch account login \
    --resource-group myResourceGroup \
    --name mybatchaccount \
    --shared-key-auth
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
| [az storage account create](https://docs.azure.cn/cli/storage/account#az_storage_account_create) | 创建存储帐户。 |
| [az batch account set](https://docs.azure.cn/cli/batch/account#az_batch_account_set) | 更新批处理帐户的属性。  |
| [az batch account show](https://docs.azure.cn/cli/batch/account#az_batch_account_show) | 检索指定批处理帐户的详细信息。  |
| [az batch account keys list](https://docs.azure.cn/cli/batch/account/keys#az_batch_account_keys_list) | 检索指定批处理帐户的访问密钥。  |
| [az batch account login](https://docs.azure.cn/cli/batch/account#az_batch_account_login) | 针对指定的批处理帐户进行身份验证，以便进一步进行 CLI 交互。  |
| [az group delete](https://docs.azure.cn/cli/group#az_group_delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.azure.cn/cli)。

<!-- Update_Description: update meta properties, wording update, update link -->