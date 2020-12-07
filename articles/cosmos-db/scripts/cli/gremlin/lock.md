---
title: 为 Azure Cosmos DB 的 Gremlin 数据库和图形创建资源锁
description: 为 Azure Cosmos DB 的 Gremlin 数据库和图形创建资源锁
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
origin.date: 07/29/2020
author: rockboyfor
ms.date: 12/07/2020
ms.testscope: yes
ms.testdate: 08/10/2020
ms.author: v-yeche
ms.openlocfilehash: 4a690a64b7cbe42e6fe93a156a13379ac9f35623
ms.sourcegitcommit: bbe4ee95604608448cf92dec46c5bfe4b4076961
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2020
ms.locfileid: "96598573"
---
<!--Verified successfully-->
# <a name="create-a-resource-lock-for-azure-cosmos-gremlin-api-database-and-graph-using-azure-cli"></a>使用 Azure CLI 为 Azure Cosmos Gremlin API 数据库和图形创建资源锁
[!INCLUDE[appliesto-gremlin-api](../../../includes/appliesto-gremlin-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 Azure CLI 版本 2.9.1 或更高版本。

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../../../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)]

> [!IMPORTANT]
> 对于使用任何 Gremlin SDK 或 Azure 门户进行连接的用户所做的更改，除非先在启用了 `disableKeyBasedMetadataWriteAccess` 属性的情况下锁定 Cosmos DB 帐户，否则资源锁无效。 要详细了解如何启用此属性，请参阅[防止 SDK 更改](../../../role-based-access-control.md#prevent-sdk-changes)。

## <a name="sample-script"></a>示例脚本

```azurecli
#!/bin/bash
# Reference: az cosmosdb | https://docs.azure.cn/cli/cosmosdb
# --------------------------------------------------
#
# Resource lock operations for a Gremlin database and graph
#
#

# Sign in the Azure China Cloud
az cloud set -n AzureChinaCloud
az login

resourceGroupName='myResourceGroup'
accountName='my-cosmos-account'
databaseName='database1'
graphName='graph1'

lockType='CanNotDelete' # CanNotDelete or ReadOnly
databaseParent="databaseAccounts/$accountName"
graphParent="databaseAccounts/$accountName/gremlinDatabases/$databaseName"
databaseLockName="$databaseName-Lock"
graphLockName="$containerName-Lock"

# Create a delete lock on database
az lock create --name $databaseLockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/gremlinDatabases \
    --lock-type $lockType \
    --parent $databaseParent \
    --resource $databaseName

# Create a delete lock on graph
az lock create --name $graphLockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/graphs \
    --lock-type $lockType \
    --parent $graphParent \
    --resource $graphName

# List all locks on a Cosmos account
az lock list --resource-group $resourceGroupName \
    --resource-name $accountName \
    --namespace Microsoft.DocumentDB \
    --resource-type databaseAccounts

# Delete lock on database
lockid=$(az lock show --name $databaseLockName \
        --resource-group $resourceGroupName \
        --resource-type Microsoft.DocumentDB/gremlinDatabases \
        --resource $databaseName --parent $databaseParent \
        --output tsv --query id)
az lock delete --ids $lockid

# Delete lock on graph
lockid=$(az lock show --name $graphLockName \
        --resource-group $resourceGroupName \
        --resource-type Microsoft.DocumentDB/graphs \
        --resource-name $graphName --parent $graphParent \
        --output tsv --query id)
az lock delete --ids $lockid

```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az lock create](https://docs.azure.cn/cli/lock#az_lock_create) | 创建锁。 |
| [az lock list](https://docs.azure.cn/cli/lock#az_lock_list) | 列出锁信息。 |
| [az lock show](https://docs.azure.cn/cli/lock#az_lock_show) | 显示锁的属性。 |
| [az lock delete](https://docs.azure.cn/cli/lock#az_lock_delete) | 删除锁。 |

## <a name="next-steps"></a>后续步骤

- [锁定资源以防止意外更改](../../../../azure-resource-manager/management/lock-resources.md)

- [Azure Cosmos DB CLI 文档](https://docs.azure.cn/cli/cosmosdb)。

- [Azure Cosmos DB CLI GitHub 存储库](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)。

<!-- Update_Description: update meta properties, wording update, update link -->