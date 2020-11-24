---
title: 创建存储帐户
titleSuffix: Azure Storage
description: 了解如何创建存储帐户以存储 blob、文件、队列和表。 Azure 存储帐户在 Microsoft Azure 中提供唯一的命名空间，用于读取和写入数据。
services: storage
author: WenJason
ms.service: storage
ms.topic: how-to
origin.date: 09/24/2020
ms.date: 11/16/2020
ms.author: v-jay
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: f61391db8a8198a2387e5970f245fdf0093f46b3
ms.sourcegitcommit: 5f07189f06a559d5617771e586d129c10276539e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94552459"
---
# <a name="create-a-storage-account"></a>创建存储帐户

Azure 存储帐户包含所有 Azure 存储数据对象：Blob、文件、队列、表和磁盘。 存储帐户为你的 Azure 存储数据提供了一个唯一的命名空间，可以从世界上的任何位置通过 HTTP 或 HTTPS 访问该命名空间。 Azure 存储帐户中的数据是持久的，高度可用、安全且可大规模缩放。

本操作指南文章介绍如何使用 [Azure 门户](https://portal.azure.cn/)、[Azure PowerShell](https://docs.microsoft.com/powershell/azure/)、[Azure CLI](/cli/) 或 [Azure 资源管理器模板](../../azure-resource-manager/management/overview.md)创建存储帐户。  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必备条件

如果没有 Azure 订阅，可在开始前创建一个 [1 元人民币试用帐户](https://wd.azure.cn/zh-cn/pricing/1rmb-trial-full)。

# <a name="portal"></a>[门户](#tab/azure-portal)

无。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 PowerShell 创建 Azure 存储帐户，请确保已安装 Azure PowerShell 模块 Az 0.7 版或更高版本。 有关详细信息，请参阅 [Azure PowerShell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az)。

若要查找当前版本，请运行以下命令：

```powershell
Get-InstalledModule -Name "Az"
```

若要安装或升级 Azure PowerShell，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-Az-ps)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

可以安装 CLI 并在本地运行 CLI 命令。

### <a name="install-the-cli-locally"></a>在本地安装 CLI

本操作指南文章要求运行 Azure CLI 2.0.4 或更高版本。 运行 `az --version` 来查找已安装的版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/install-azure-cli)。 

# <a name="template"></a>[模板](#tab/template)

无。

---

## <a name="sign-in-to-azure"></a>登录 Azure

# <a name="portal"></a>[门户](#tab/azure-portal)

登录 [Azure 门户](https://portal.azure.cn)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 `Connect-AzAccount` 命令登录到 Azure 订阅，然后按照屏幕上的说明进行身份验证。

```powershell
Connect-AzAccount -Environment AzureChinaCloud
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要登录到本地安装的 CLI，请运行 [az login](/cli/reference-index#az-login) 命令：

```azurecli
az login
```

# <a name="template"></a>[模板](#tab/template)

空值

---

## <a name="create-a-storage-account"></a>创建存储帐户

每个存储帐户都必须属于 Azure 资源组。 资源组是对 Azure 资源进行分组的逻辑容器。 在创建存储帐户时，可以选择创建新的资源组，也可以使用现有资源组。 本文介绍如何创建新资源组。

可以使用常规用途 v2 存储帐户访问所有 Azure 存储服务：Blob、文件、队列、表和磁盘。 本文所述的步骤将创建常规用途 v2 存储帐户，但创建任何类型的存储帐户的步骤都相似。 有关存储帐户类型和其他存储帐户设置的详细信息，请参阅 [Azure 存储帐户概述](storage-account-overview.md)。

# <a name="portal"></a>[门户](#tab/azure-portal)

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

首先，使用 [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) 命令，通过 PowerShell 创建新的资源组：

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hard-coding it repeatedly
$resourceGroup = "storage-resource-group"
$location = "chinaeast2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

如果不确定为 `-Location` 参数指定哪个区域，可使用 [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) 命令检索订阅支持的区域的列表：

```powershell
Get-AzLocation | select Location
```

接下来，通过 [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/New-azStorageAccount) 命令创建使用读取访问异地冗余存储 (RA-GRS) 的常规用途 v2 存储帐户。 请记住，存储帐户的名称必须在 Azure 中唯一，因此请将括号中的占位符值替换为你自己的唯一值：

```powershell
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2
```

> [!IMPORTANT]
> 如果计划使用 [Azure Data Lake Storage](/storage/blobs/data-lake-storage-introduction)，请在此参数列表中包含 `-EnableHierarchicalNamespace $True`。

若要使用不同的复制选项创建常规用途 v2 存储帐户，请将 **SkuName** 参数替换为下表中的所需值。

|复制选项  |SkuName 参数  |
|---------|---------|
|本地冗余存储 (LRS)     |Standard_LRS         |
|异地冗余存储 (GRS)     |Standard_GRS         |
|读取访问异地冗余存储 (GRS)     |Standard_RAGRS         |

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

首先，使用 [az group create](/cli/group#az_group_create) 命令，通过 Azure CLI 创建新的资源组。

```azurecli
az group create \
    --name storage-resource-group \
    --location chinaeast2
```

如果不确定为 `--location` 参数指定哪个区域，可使用 [az account list-locations](/cli/account#az_account_list) 命令检索订阅支持的区域的列表。

```azurecli
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

接下来，通过 [az storage account create](/cli/storage/account#az_storage_account_create) 命令创建使用读取访问异地冗余存储的常规用途 v2 存储帐户。 请记住，存储帐户的名称必须在 Azure 中唯一，因此请将括号中的占位符值替换为你自己的唯一值：

```azurecli
az storage account create \
    --name <account-name> \
    --resource-group storage-resource-group \
    --location chinaeast2 \
    --sku Standard_RAGRS \
    --kind StorageV2
```

> [!IMPORTANT]
> 如果计划使用 [Azure Data Lake Storage](/storage/blobs/data-lake-storage-introduction)，请在此参数列表中包含 `--enable-hierarchical-namespace true`。 

若要使用不同的复制选项创建常规用途 v2 存储帐户，请将 **sku** 参数替换为下表中的所需值。

|复制选项  |sku 参数  |
|---------|---------|
|本地冗余存储 (LRS)     |Standard_LRS         |
|异地冗余存储 (GRS)     |Standard_GRS         |
|读取访问异地冗余存储 (GRS)     |Standard_RAGRS         |

# <a name="template"></a>[模板](#tab/template)

可以使用 Azure PowerShell 或 Azure CLI 来部署资源管理器模板以创建存储帐户。 本操作指南文章中使用的模板来自 [Azure 资源管理器快速入门模板](https://azure.microsoft.com/resources/templates/101-storage-account-create/)。

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. chinaeast2)"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

```azurecli
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location "$location" &&
az group deployment create --resource-group $resourceGroupName --template-file "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

> [!NOTE]
> 此模板仅用作示例。 有许多存储帐户设置未在此模板中进行配置。 例如，如果想要使用 [Azure Data Lake Storage](/storage/blobs/data-lake-storage-introduction)，可以通过将 `StorageAccountPropertiesCreateParameters` 对象的 `isHnsEnabledad` 属性设为 `true` 来修改此模板。 

若要了解如何修改此模板或创建新模板，请参阅：

- [Azure 资源管理器文档](../../azure-resource-manager/index.yml)。
- [存储帐户模板参考](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions)。
- [其他存储帐户模板示例](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage)。

---

## <a name="delete-a-storage-account"></a>删除存储帐户

删除存储帐户将删除整个帐户，包括该帐户中的所有数据，并且该操作无法撤销。

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. 在 [Azure 门户](https://portal.azure.cn)中导航到存储帐户。
1. 单击 **“删除”** 。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要删除存储帐户，请使用 [Remove-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/remove-azstorageaccount) 命令：

```powershell
Remove-AzStorageAccount -Name <storage-account> -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要删除存储帐户，请使用 [az storage account delete](/cli/storage/account#az-storage-account-delete) 命令：

```azurecli
az storage account delete --name <storage-account> --resource-group <resource-group>
```

# <a name="template"></a>[模板](#tab/template)

若要删除存储帐户，请使用 Azure PowerShell 或 Azure CLI。

```azurepowershell
$storageResourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
Remove-AzStorageAccount -Name $storageAccountName -ResourceGroupName $storageResourceGroupName
```

```azurecli
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --name storageAccountName --resource-group resourceGroupName
```

---

或者，你可以删除资源组，该操作将删除该资源组中的存储帐户和其他任何资源。 有关删除资源组的详细信息，请参阅[删除资源组和资源](../../azure-resource-manager/management/delete-resource-group.md)。

> [!WARNING]
> 无法恢复已删除的存储帐户，也无法检索删除之前该存储帐户包含的任何内容。 请在删除帐户之前务必备份要保存的任何内容。 对于帐户中的任务资源也是如此 — 一旦你删除了一个 Blob、表、队列或文件，则它会被永久删除。
>
> 如果尝试删除与 Azure 虚拟机关联的存储帐户，则会显示一条错误消息，指出存储帐户仍在使用。 有关排除此错误的帮助信息，请参阅[删除存储帐户时排除错误](../../virtual-machines/troubleshooting/index.yml)。

## <a name="next-steps"></a>后续步骤

- [存储帐户概述](storage-account-overview.md)
- [升级到常规用途 v2 存储帐户](storage-account-upgrade.md)
- [将 Azure 存储帐户移到另一个区域](storage-account-move.md)