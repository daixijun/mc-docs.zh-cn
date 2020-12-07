---
title: 创建块 Blob 存储帐户 - Azure 存储 | Microsoft Docs
description: 演示如何创建具有高级性能特征的 Azure BlockBlobStorage 帐户。
author: WenJason
services: storage
ms.service: storage
ms.topic: how-to
origin.date: 10/30/2020
ms.date: 11/16/2020
ms.author: v-jay
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 444a3772ea107b16e8897ac40136256492db463f
ms.sourcegitcommit: 5df3a4ca29d3cb43b37f89cf03c1aa74d2cd4ef9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96432037"
---
# <a name="create-a-blockblobstorage-account"></a>创建 BlockBlobStorage 帐户

BlockBlobStorage 帐户类型允许创建具有高级性能特征的块 Blob。 此类存储帐户针对事务处理速率高或需要极快速访问的工作负载进行了优化。 本文介绍如何使用 Azure 门户、Azure CLI 或 Azure PowerShell 创建 BlockBlobStorage 帐户。

有关 BlockBlobStorage 帐户的详细信息，请参阅 [Azure 存储帐户概述](/storage/common/storage-account-overview)。

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

# <a name="portal"></a>[Portal](#tab/azure-portal)

无。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

本操作指南文章需要 Azure PowerShell 模块 Az 1.2.0 或更高版本。 运行 `Get-Module -ListAvailable Az` 即可查找当前版本。 如果需要进行安装或升级，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-Az-ps)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

可以登录到 Azure，然后在本地运行 Azure CLI 命令。

### <a name="install-the-cli-locally"></a>在本地安装 CLI

也可在本地安装和使用 Azure CLI。 本操作指南文章要求运行 Azure CLI 2.0.46 或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/install-azure-cli)。 

---

## <a name="sign-in-to-azure"></a>登录 Azure

# <a name="portal"></a>[Portal](#tab/azure-portal)

登录到 [Azure 门户](https://portal.azure.cn)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 `Connect-AzAccount` 命令登录到 Azure 订阅，然后按照屏幕上的说明进行身份验证。

```powershell
Connect-AzAccount -Environment AzureChinaCloud
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要启动 Azure Cloud Shell，请登录到 [Azure 门户](https://portal.azure.cn)。

若要登录到本地安装的 CLI，请运行 [az login](/cli/reference-index#az-login) 命令：

```azurecli
az login
```

---

## <a name="create-a-blockblobstorage-account"></a>创建 BlockBlobStorage 帐户

## <a name="portal"></a>[Portal](#tab/azure-portal)
若要在 Azure 门户中创建 BlockBlobStorage 帐户，请执行以下步骤：

1. 在 Azure 门户中选择“所有服务”>“存储”类别 >“存储帐户”  。

2. 在“存储帐户”下选择“添加”。 

3. 在“订阅”字段中，选择要在其中创建存储帐户的订阅。

4. 在“资源组”字段中，选择一个现有资源组，或者选择“新建”并输入新资源组的名称。 

5. 在“存储帐户名称”字段中，输入帐户的名称。 注意以下准则：

   - 该名称在 Azure 中必须唯一。
   - 名称的长度必须介于 3 到 24 个字符之间。
   - 名称只能包含数字和小写字母。

6. 在“位置”字段中选择存储帐户的位置，或使用默认位置。

7. 对于其余设置，请配置以下各项：

   |字段     |Value  |
   |---------|---------|
   |**“性能”**    |  选择“高级”。   |
   |帐户类型    | 选择“BlockBlobStorage”。      |
   |**复制**    |  保留默认设置“本地冗余存储(LRS)”。      |

   ![显示用于创建块 Blob 存储帐户的门户 UI](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

8. 选择“高级”选项卡。

9. 若要优化用于数据分析的存储帐户，请将“分层命名空间”设为“启用”。 否则，请将此选项设置为默认值。 通过 BlockBlobStorage 帐户启用此设置时，将为你提供 [Data Lake Storage 的高级层](premium-tier-for-data-lake-storage.md)。  若要详细了解 Data Lake Storage，请参阅 [Azure Data Lake Storage Gen2 简介](data-lake-storage-introduction.md)。

8. 选择“查看+创建”可查看存储帐户设置。

9. 选择“创建” 。

## <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. 打开提升的 Windows PowerShell 会话（以管理员身份运行）。

2. 运行以下命令，确保安装 `Az` PowerShell 模块的最新版本。

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

3. 打开新的 PowerShell 控制台，使用 Azure 帐户登录。

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID> -Environment AzureChinaCloud
   ```

4. 如果需要，请创建新的资源组。 替换这些带引号的值并运行以下命令。

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

5. 创建 BlockBlobStorage 帐户。 替换这些带引号的值并运行以下命令。

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```
   若要优化用于数据分析的存储帐户，请将 `-EnableHierarchicalNamespace $True` 添加到命令中。 通过 BlockBlobStorage 帐户启用此设置时，将为你提供 [Data Lake Storage 的高级层](premium-tier-for-data-lake-storage.md)。  若要详细了解 Data Lake Storage，请参阅 [Azure Data Lake Storage Gen2 简介](data-lake-storage-introduction.md)。

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 创建块 Blob 帐户，必须先安装 Azure CLI 版本 2.0.46 或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/install-azure-cli)。

1. 登录到 Azure 订阅。

   ```azurecli
   az login
   ```

2. 如果需要，请创建新的资源组。 替换带括号的值（包括括号），并运行以下命令。

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

3. 创建 BlockBlobStorage 帐户。 替换带括号的值（包括括号），并运行以下命令。

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

   若要优化用于数据分析的存储帐户，请将 `--hierarchical-namespace true` 添加到命令中。 通过 BlockBlobStorage 帐户启用此设置时，将为你提供 [Data Lake Storage 的高级层](premium-tier-for-data-lake-storage.md)。  若要详细了解 Data Lake Storage，请参阅 [Azure Data Lake Storage Gen2 简介](data-lake-storage-introduction.md)。

---

## <a name="next-steps"></a>后续步骤

- 有关存储帐户的详细信息，请参阅 [Azure 存储帐户概述](/storage/common/storage-account-overview)。

- 有关资源组的详细信息，请参阅 [Azure 资源管理器概述](/azure-resource-manager/resource-group-overview)。
