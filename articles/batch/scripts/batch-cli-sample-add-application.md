---
title: Azure CLI 脚本示例 - 在 Batch 中添加应用程序
description: 此示例脚本演示如何添加要与 Azure Batch 池或任务配合使用的应用程序。
ms.topic: sample
origin.date: 01/29/2018
author: rockboyfor
ms.date: 11/23/2020
ms.testscope: no
ms.testdate: 09/07/2018
ms.author: v-yeche
ms.custom: devx-track-azurecli
ms.openlocfilehash: 11ecbf6fd9937d35b670eca8fa1a8d33fc6101ae
ms.sourcegitcommit: c2c9dc65b886542d220ae17afcb1d1ab0a941932
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "95970720"
---
<!--Verified successfully-->
# <a name="cli-example-add-an-application-to-an-azure-batch-account"></a>CLI 示例：向 Azure Batch 帐户添加应用程序

此脚本演示如何添加要与 Azure Batch 池或任务配合使用的应用程序。 若要设置添加到 Batch 帐户的应用程序，请将可执行文件与所有依赖文件一起打包为 zip 文件。 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 本教程需要 Azure CLI 版本 2.0.20 或更高版本。

<!--Not Available on Azure Cloud Shell-->

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)]

## <a name="example-script"></a>示例脚本

<!--MOONCAKE: --application-name MUST MATCH [A-Za-z-]-->
<!--MOONCAKE: KEEP ALIGN WITH CHINAEAST-->

```azurecli
#!/bin/bash

# Create a resource group.
az group create --name myResourceGroup --location chinaeast

# Create a general-purpose storage account in your resource group.
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --location chinaeast \
    --sku Standard_LRS

# Create a Batch account.
az batch account create \
    --name mybatchaccount \
    --storage-account mystorageaccount \
    --resource-group myResourceGroup \
    --location chinaeast

# Authenticate against the account directly for further CLI interaction.
az batch account login \
    --name mybatchaccount \
    --resource-group myResourceGroup \
    --shared-key-auth

# Create a new application.
az batch application create \
    --resource-group myResourceGroup \
    --name mybatchaccount \
    --application-name "My-Application"

# An application can reference multiple application executable packages
# of different versions. The executables and any dependencies need
# to be zipped up for the package. Once uploaded, the CLI attempts
# to activate the package so that it's ready for use.
az batch application package create \
    --resource-group myResourceGroup \
    --name mybatchaccount \
    --application-name "My-Application" \
    --package-file my-application-exe.zip \
    --version-name 1.0

# Update the application to assign the newly added application
# package as the default version.
az batch application set \
    --resource-group myResourceGroup \
    --name mybatchaccount \
    --application-name "My-Application" \
    --default-version 1.0
```

## <a name="clean-up-deployment"></a>清理部署

运行以下命令以删除资源组及其相关的所有资源。

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。
表中的每条命令链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [az group create](https://docs.azure.cn/cli/group#az_group_create) | 创建用于存储所有资源的资源组。 |
| [az storage account create](https://docs.azure.cn/cli/storage/account#az_storage_account_create) | 创建存储帐户。 |
| [az batch account create](https://docs.azure.cn/cli/batch/account#az_batch_account_create) | 创建批处理帐户。 |
| [az batch account login](https://docs.azure.cn/cli/batch/account#az_batch_account_login) | 针对指定的批处理帐户进行身份验证，以便进一步进行 CLI 交互。  |
| [az batch application create](https://docs.azure.cn/cli/batch/application#az_batch_application_create) | 创建应用程序。  |
| [az batch application package create](https://docs.azure.cn/cli/batch/application/package#az_batch_application_package_create) | 将应用程序包添加到指定的应用程序。  |
| [az batch application set](https://docs.azure.cn/cli/batch/application#az_batch_application_set) | 更新应用程序的属性。  |
| [az group delete](https://docs.azure.cn/cli/group#az_group_delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.azure.cn/cli)。

<!-- Update_Description: update meta properties, wording update, update link -->