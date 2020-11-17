---
title: 用于 Azure Cosmos DB MongoDB API 资源的吞吐量 (RU/s) 操作的 Azure CLI 脚本
description: 用于 Azure Cosmos DB MongoDB API 资源的吞吐量 (RU/s) 操作的 Azure CLI 脚本
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
origin.date: 10/07/2020
author: rockboyfor
ms.date: 11/09/2020
ms.testscope: yes
ms.testdate: 08/10/2020
ms.author: v-yeche
ms.openlocfilehash: 7ac5e7d764e51231e9ba4a3600c5e2bd030d30b8
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94328059"
---
<!--Verified successfully-->
# <a name="throughput-rus-operations-with-azure-cli-for-a-database-or-graph-for-azure-cosmos-db-api-for-mongodb"></a>针对用于 Azure Cosmos DB MongoDB API 的数据库或图形，使用 Azure CLI 实现的吞吐量 (RU/s) 操作
[!INCLUDE[appliesto-mongodb-api](../../../includes/appliesto-mongodb-api.md)]

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../../../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)]

如果选择在本地安装并使用 CLI，本主题要求运行 Azure CLI 2.12.1 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](https://docs.azure.cn/cli/install-azure-cli)。

## <a name="sample-script"></a>示例脚本

此脚本创建一个具有共享吞吐量的 MongoDB 数据库和一个具有专用吞吐量的集合，然后更新该数据库和该集合的吞吐量。 脚本随后从标准吞吐量迁移到自动缩放吞吐量，然后在迁移后读取自动缩放吞吐量的值。

```azurecli
#!/bin/bash
# Reference: az cosmosdb | https://docs.azure.cn/cli/cosmosdb
# --------------------------------------------------
#
# Throughput operations for a MongoDB API database and collection
#
#

# Sign in the Azure China Cloud
az cloud set -n AzureChinaCloud
az login

# Generate a unique 10 character alphanumeric string to ensure unique resource names
uniqueId=$(env LC_CTYPE=C tr -dc 'a-z0-9' < /dev/urandom | fold -w 10 | head -n 1)

# Variables for MongoDB API resources
resourceGroupName="Group-$uniqueId"
location='chinanorth2'
accountName="cosmos-$uniqueId" #needs to be lower case
databaseName='database1'
collectionName='collection1'
originalThroughput=400
updateThroughput=500

# Create a resource group, Cosmos account, database and collection
az group create -n $resourceGroupName -l $location
az cosmosdb create -n $accountName -g $resourceGroupName --kind MongoDB
az cosmosdb mongodb database create -a $accountName -g $resourceGroupName -n $databaseName --throughput $originalThroughput

# Define a minimal index policy for the collection
idxpolicy=$(cat << EOF 
    [ {"key": {"keys": ["user_id"]}} ]
EOF
)
echo "$idxpolicy" > "idxpolicy-$uniqueId.json"

# Create a MongoDB API collection
az cosmosdb mongodb collection create -a $accountName -g $resourceGroupName -d $databaseName -n $collectionName --shard 'user_id' --throughput $originalThroughput --idx @idxpolicy-$uniqueId.json
# Clean up temporary index policy file
rm -f "idxpolicy-$uniqueId.json"

# Throughput operations for MongoDB API database
# Read the current throughput
# Read the minimum throughput
# Make sure the updated throughput is not less than the minimum
# Update the throughput
# Migrate between standard (manual) and autoscale throughput
# Read the autoscale max throughput

read -p 'Press any key to read current provisioned throughput on database'

az cosmosdb mongod database throughput show \
    -g $resourceGroupName \
    -a $accountName \
    -n $databaseName \
    --query resource.throughput \
    -o tsv

read -p 'Press any key to read minimum throughput on database'

minimumThroughput=$(az cosmosdb mongodb database throughput show \
    -g $resourceGroupName \
    -a $accountName \
    -n $databaseName \
    --query resource.minimumThroughput \
    -o tsv)

echo $minimumThroughput

# Make sure the updated throughput is not less than the minimum allowed throughput
if [ $updateThroughput -lt $minimumThroughput ]; then
    updateThroughput=$minimumThroughput
fi

read -p 'Press any key to update Database throughput'

az cosmosdb mongodb database throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    --throughput $updateThroughput

read -p 'Press any key to migrate the database from standard (manual) throughput to autoscale throughput'

az cosmosdb mongodb database throughput migrate \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    -t 'autoscale'

read -p 'Press any key to read current autoscale provisioned max throughput on the database'

az cosmosdb mongodb database throughput show \
    -g $resourceGroupName \
    -a $accountName \
    -n $databaseName \
    --query resource.autoscaleSettings.maxThroughput \
    -o tsv

# Throughput operations for MongoDB API collection
# Read the current throughput
# Read the minimum throughput
# Make sure the updated throughput is not less than the minimum
# Update the throughput
# Migrate between standard (manual) and autoscale throughput
# Read the autoscale max throughput

read -p 'Press any key to read current provisioned throughput on collection'

az cosmosdb mongodb collection throughput show \
    -a $accountName \
    -g $resourceGroupName \
    -d $databaseName \
    -n $collectionName \
    --query resource.throughput \
    -o tsv

read -p 'Press any key to read minimum throughput on collection'

minimumThroughput=$(az cosmosdb mongodb collection throughput show \
    -a $accountName \
    -g $resourceGroupName \
    -d $databaseName \
    -n $collectionName \
    --query resource.minimumThroughput \
    -o tsv)

echo $minimumThroughput

# Make sure the updated throughput is not less than the minimum allowed throughput
if [ $updateThroughput -lt $minimumThroughput ]; then
    updateThroughput=$minimumThroughput
fi

read -p 'Press any key to update collection throughput'

az cosmosdb mongodb collection throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -d $databaseName \
    -n $collectionName \
    --throughput $updateThroughput

read -p 'Press any key to migrate the collection from standard (manual) throughput to autoscale throughput'

az cosmosdb sql container throughput migrate \
    -a $accountName \
    -g $resourceGroupName \
    -d $databaseName \
    -n $collectionName \
    -t 'autoscale'

read -p 'Press any key to read current autoscale provisioned max throughput on the collection'

az cosmosdb sql container throughput show \
    -g $resourceGroupName \
    -a $accountName \
    -d $databaseName \
    -n $collectionName \
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
| [az cosmosdb mongodb database create](https://docs.azure.cn/cli/cosmosdb/mongodb/database#az_cosmosdb_mongodb_database_create) | 创建 Azure Cosmos MongoDB API 数据库。 |
| [az cosmosdb mongodb collection create](https://docs.azure.cn/cli/cosmosdb/mongodb/collection#az_cosmosdb_mongodb_collection_create) | 创建 Azure Cosmos MongoDB API 集合。 |
| [az cosmosdb mongodb database throughput update](https://docs.azure.cn/cli/cosmosdb/mongodb/database/throughput#az_cosmosdb_mongodb_database_throughput_update) | 更新 Azure Cosmos MongoDB API 数据库的 RU。 |
| [az cosmosdb mongodb collection throughput update](https://docs.azure.cn/cli/cosmosdb/mongodb/collection/throughput#az_cosmosdb_mongodb_collection_throughput_update) | 更新 Azure Cosmos MongoDB API 集合的 RU。 |
| [az cosmosdb mongodb database throughput migrate](https://docs.azure.cn/cli/cosmosdb/mongodb/database/throughput#az_cosmosdb_mongodb_database_throughput_migrate) | 迁移数据库的吞吐量。 |
| [az cosmosdb mongodb collection throughput migrate](https://docs.azure.cn/cli/cosmosdb/mongodb/collection/throughput#az_cosmosdb_mongodb_collection_throughput_migrate) | 迁移集合的吞吐量。 |
| [az group delete](https://docs.azure.cn/cli/group#az_group_delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure Cosmos DB CLI 的详细信息，请参阅 [Azure Cosmos DB CLI 文档](https://docs.azure.cn/cli/cosmosdb)。

可以在 [Azure Cosmos DB CLI GitHub 存储库](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)中找到所有 Azure Cosmos DB CLI 脚本示例。

<!-- Update_Description: update meta properties, wording update, update link -->