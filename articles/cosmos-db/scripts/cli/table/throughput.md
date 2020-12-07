---
title: 用于 Azure Cosmos DB 表 API 资源的吞吐量 (RU/s) 操作的 Azure CLI 脚本
description: 用于 Azure Cosmos DB 表 API 资源的吞吐量 (RU/s) 操作的 Azure CLI 脚本
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
origin.date: 10/07/2020
author: rockboyfor
ms.date: 12/07/2020
ms.author: v-yeche
ms.openlocfilehash: 826565876e5cf709c20c3719a3c4e90d1581a7ca
ms.sourcegitcommit: bbe4ee95604608448cf92dec46c5bfe4b4076961
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2020
ms.locfileid: "96598681"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-table-for-azure-cosmos-db-table-api"></a>用于 Azure Cosmos DB 表 API 的表的使用 Azure CLI 的吞吐量 (RU/s) 操作
[!INCLUDE[appliesto-table-api](../../../includes/appliesto-table-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 Azure CLI 版本 2.12.1 或更高版本。

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../../../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)]

## <a name="sample-script"></a>示例脚本

此脚本创建一个表 API 表，然后更新该表的吞吐量。 脚本随后从标准吞吐量迁移到自动缩放吞吐量，然后在迁移后读取自动缩放吞吐量的值。

```azurecli
#!/bin/bash
# Reference: az cosmosdb | https://docs.azure.cn/cli/cosmosdb
# --------------------------------------------------
#
# Throughput operations for a Table API table
#
#

# Sign in the Azure China Cloud
az cloud set -n AzureChinaCloud
az login

# Variables for Cassandra API resources
uniqueId=$RANDOM
resourceGroupName="Group-$uniqueId"
location='chinanorth2'
accountName="cosmos-$uniqueId" #needs to be lower case
tableName='table1'
originalThroughput=400
updateThroughput=500

# Create a resource group, Cosmos account and table
az group create -n $resourceGroupName -l $location
az cosmosdb create -n $accountName -g $resourceGroupName --capabilities EnableTable
az cosmosdb table create -a $accountName -g $resourceGroupName -n $tableName --throughput $originalThroughput

# Throughput operations for Table API table
# Read the current throughput
# Read the minimum throughput
# Make sure the updated throughput is not less than the minimum
# Update the throughput
# Migrate between standard (manual) and autoscale throughput
# Read the autoscale max throughput

read -p 'Press any key to get current provisioned table throughput'

az cosmosdb table throughput show \
    -a $accountName \
    -g $resourceGroupName \
    -n $tableName \
    --query resource.throughput \
    -o tsv

read -p 'Press any key to get minimum allowable table throughput'

minimumThroughput=$(az cosmosdb table throughput show \
    -a $accountName \
    -g $resourceGroupName \
    -n $tableName \
    --query resource.minimumThroughput \
    -o tsv)

echo $minimumThroughput

# Make sure the updated throughput is not less than the minimum allowed throughput
if [ $updateThroughput -lt $minimumThroughput ]; then
    updateThroughput=$minimumThroughput
fi

read -p 'Press any key to update table throughput'

az cosmosdb table throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -n $tableName \
    --throughput $updateThroughput

read -p 'Press any key to migrate the table from standard (manual) throughput to autoscale throughput'

az cosmosdb table throughput migrate \
    -a $accountName \
    -g $resourceGroupName \
    -n $tableName \
    -t 'autoscale'

read -p 'Press any key to read current autoscale provisioned max throughput on the table'

az cosmosdb table throughput show \
    -g $resourceGroupName \
    -a $accountName \
    -n $tableName \
    --query resource.autoscaleSettings.maxThroughput \
    -o tsv

```

## <a name="clean-up-deployment"></a>清理部署

运行脚本示例后，可以使用以下命令删除资源组以及与其关联的所有资源。

```azurecli
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](https://docs.azure.cn/cli/group#az_group_create) | 创建用于存储所有资源的资源组。 |
| [az cosmosdb create](https://docs.azure.cn/cli/cosmosdb#az_cosmosdb_create) | 创建 Azure Cosmos DB 帐户。 |
| [az cosmosdb table create](https://docs.azure.cn/cli/cosmosdb/table#az_cosmosdb_table_create) | 创建 Azure Cosmos 表 API 表。 |
| [az cosmosdb table throughput update](https://docs.azure.cn/cli/cosmosdb/table/throughput#az_cosmosdb_table_throughput_update) | 更新 Azure Cosmos 表 API 表的吞吐量。 |
| [az cosmosdb table throughput migrate](https://docs.azure.cn/cli/cosmosdb/table/throughput#az_cosmosdb_table_throughput_migrate) | 迁移 Azure Cosmos 表 API 表的吞吐量。 |
| [az group delete](https://docs.azure.cn/cli/group#az_group_delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure Cosmos DB CLI 的详细信息，请参阅 [Azure Cosmos DB CLI 文档](https://docs.azure.cn/cli/cosmosdb)。

可以在 [Azure Cosmos DB CLI GitHub 存储库](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)中找到所有 Azure Cosmos DB CLI 脚本示例。

<!-- Update_Description: update meta properties, wording update, update link -->