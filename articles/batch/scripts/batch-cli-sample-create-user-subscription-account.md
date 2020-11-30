---
title: Azure CLI 脚本示例 - 创建 Batch 帐户 - 用户订阅
description: 此脚本在用户订阅模式下创建 Azure Batch 帐户。 此帐户将计算节点分配到你的订阅。
ms.topic: sample
origin.date: 01/29/2018
author: rockboyfor
ms.date: 11/23/2020
ms.testscope: no
ms.testdate: ''
ms.author: v-yeche
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4f88c022bae05a092fe6130f9f7e1c0afb60eda6
ms.sourcegitcommit: c2c9dc65b886542d220ae17afcb1d1ab0a941932
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "95970718"
---
# <a name="cli-example-create-a-batch-account-in-user-subscription-mode"></a>CLI 示例：在用户订阅模式下创建 Batch 帐户

此脚本在用户订阅模式下创建 Azure Batch 帐户。 必须通过 Azure Active Directory 令牌对那些将计算节点分配到订阅中的帐户进行身份验证。 计算节点将计数分配到订阅的 vCPU（核心）配额。 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 本教程需要 Azure CLI 版本 2.0.20 或更高版本。

<!--Not Available on Azure Cloud Shell-->

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)]

## <a name="example-script"></a>示例脚本

```azurecli
#!/bin/bash

# Allow Azure Batch to access the subscription (one-time operation).
az role assignment create \
    --assignee MicrosoftAzureBatch \
    --role contributor

# Create a resource group.
az group create --name myResourceGroup --location chinanorth

# Create an Azure Key Vault. A Batch account that allocates pools in the user's subscription 
# must be configured with a Key Vault located in the same region. 
az keyvault create \
    --resource-group myResourceGroup \
    --name mykevault \
    --location chinanorth \
    --enabled-for-deployment true \
    --enabled-for-disk-encryption true \
    --enabled-for-template-deployment true

# Add an access policy to the Key Vault to allow access by the Batch Service.
az keyvault set-policy \
    --resource-group myResourceGroup \
    --name mykevault \
    --spn ddbf3205-c6bd-46ae-8127-60eb93363864 \
    --key-permissions all \
    --secret-permissions all

# Create the Batch account, referencing the Key Vault either by name (if they
# exist in the same resource group) or by its full resource ID.
az batch account create \
    --resource-group myResourceGroup \
    --name mybatchaccount \
    --location chinanorth \
    --keyvault mykevault

# Authenticate directly against the account for further CLI interaction.
# Batch accounts that allocate pools in the user's subscription must be
# authenticated via an Azure Active Directory token.
az batch account login -g myResourceGroup -n mybatchaccount

```

## <a name="clean-up-deployment"></a>清理部署

运行以下命令以删除资源组及其相关的所有资源。

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az role assignment create](https://docs.azure.cn/cli/role#az-role-assignment-create) | 为用户、组或服务主体创建新的角色分配。 |
| [az group create](https://docs.azure.cn/cli/group#az_group_create) | 创建用于存储所有资源的资源组。 |
| [az keyvault create](https://docs.azure.cn/cli/keyvault#az_keyvault_create) | 创建密钥保管库。 |
| [az keyvault set-policy](https://docs.azure.cn/cli/keyvault#az_keyvault_set_policy) | 更新指定 Key Vault 的安全策略。 |
| [az batch account create](https://docs.azure.cn/cli/batch/account#az_batch_account_create) | 创建批处理帐户。  |
| [az batch account login](https://docs.azure.cn/cli/batch/account#az_batch_account_login) | 针对指定的批处理帐户进行身份验证，以便进一步进行 CLI 交互。  |
| [az group delete](https://docs.azure.cn/cli/group#az_group_delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.azure.cn/cli)。

<!-- Update_Description: update meta properties, wording update, update link -->