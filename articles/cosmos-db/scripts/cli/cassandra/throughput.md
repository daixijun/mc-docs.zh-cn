---
title: 用于 Azure Cosmos DB Cassandra API 资源的吞吐量 (RU/s) 操作的 Azure CLI 脚本
description: 用于 Azure Cosmos DB Cassandra API 资源的吞吐量 (RU/s) 操作的 Azure CLI 脚本
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
origin.date: 10/07/2020
author: rockboyfor
ms.date: 11/09/2020
ms.testscope: yes
ms.testdate: 08/10/2020
ms.author: v-yeche
ms.openlocfilehash: 0ade1fad26776c1a3b208eda4dfe0d91c52e9618
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94328769"
---
<!--Verify successfully-->
# <a name="throughput-rus-operations-with-azure-cli-for-a-keyspace-or-table-for-azure-cosmos-db---cassandra-api"></a>使用 Azure CLI 对 Azure Cosmos DB 的密钥空间或表执行吞吐量 (RU/s) 操作 - Cassandra API
[!INCLUDE[appliesto-cassandra-api](../../../includes/appliesto-cassandra-api.md)]

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../../../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)]

如果选择在本地安装并使用 CLI，本主题要求运行 Azure CLI 2.12.1 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](https://docs.azure.cn/cli/install-azure-cli)。

## <a name="sample-script"></a>示例脚本

此脚本创建一个具有共享吞吐量的 Cassandra 密钥空间和一个具有专用吞吐量的 Cassandra 表，并更新该密钥空间和该表的吞吐量。 脚本随后从标准吞吐量迁移到自动缩放吞吐量，然后在迁移后读取自动缩放吞吐量的值。

```azurecli
#!/bin/bash
# Reference: az cosmosdb | https://docs.azure.cn/cli/cosmosdb
# --------------------------------------------------
#
# Throughput operations for a Cassandra keyspace and table
#
#

# Sign in the Azure China Cloud
az cloud set -n AzureChinaCloud
az login

# Generate a unique 10 character alphanumeric string to ensure unique resource names
uniqueId=$(env LC_CTYPE=C tr -dc 'a-z0-9' < /dev/urandom | fold -w 10 | head -n 1)

# Variables for Cassandra API resources
resourceGroupName="Group-$uniqueId"
location='chinanorth2'
accountName="cosmos-$uniqueId" #needs to be lower case
keySpaceName='keyspace1'
tableName='table1'
originalThroughput=400
updateThroughput=500

# Create a resource group, Cosmos account, keyspace and table
az group create -n $resourceGroupName -l $location
az cosmosdb create -n $accountName -g $resourceGroupName --capabilities EnableCassandra
az cosmosdb cassandra keyspace create -a $accountName -g $resourceGroupName -n $keySpaceName --throughput $originalThroughput

# Define the schema for the table and create the table
schema=$(cat << EOF 
{
    "columns": [
        {"name": "columnA","type": "uuid"}, 
        {"name": "columnB","type": "text"}
    ],
    "partitionKeys": [{"name": "columnA"}]
} 
EOF )
echo "$schema" > "schema-$uniqueId.json"
az cosmosdb cassandra table create -a $accountName -g $resourceGroupName -k $keySpaceName -n $tableName --throughput $originalThroughput --schema @schema-$uniqueId.json
rm -f "schema-$uniqueId.json"

# Throughput operations for Cassandra API keyspace
# Read the current throughput
# Read the minimum throughput
# Make sure the updated throughput is not less than the minimum
# Update the throughput
# Migrate between standard (manual) and autoscale throughput
# Read the autoscale max throughput

read -p 'Press any key to get current provisioned Keyspace throughput'

az cosmosdb cassandra keyspace throughput show \
    -a $accountName \
    -g $resourceGroupName \
    -n $keySpaceName \
    --query resource.throughput \
    -o tsv

read -p 'Press any key to get minimum allowable Keyspace throughput'

minimumThroughput=$(az cosmosdb cassandra keyspace throughput show \
    -a $accountName \
    -g $resourceGroupName \
    -n $keySpaceName \
    --query resource.minimumThroughput \
    -o tsv)

echo $minimumThroughput

# Make sure the updated throughput is not less than the minimum allowed throughput
if [ $updateThroughput -lt $minimumThroughput ]; then
    updateThroughput=$minimumThroughput
fi

read -p 'Press any key to update Keyspace throughput'

az cosmosdb cassandra keyspace throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -n $keySpaceName \
    --throughput $updateThroughput

read -p 'Press any key to migrate the keyspace from standard (manual) throughput to autoscale throughput'

az cosmosdb cassandra keyspace throughput migrate \
    -a $accountName \
    -g $resourceGroupName \
    -n $keySpaceName \
    -t 'autoscale'

read -p 'Press any key to read current autoscale provisioned max throughput on the keyspace'

az cosmosdb cassandra keyspace throughput show \
    -g $resourceGroupName \
    -a $accountName \
    -n $keySpaceName \
    --query resource.autoscaleSettings.maxThroughput \
    -o tsv

# Throughput operations for Cassandra API table
# Read the current throughput
# Read the minimum throughput
# Make sure the updated throughput is not less than the minimum
# Update the throughput
# Migrate between standard (manual) and autoscale throughput
# Read the autoscale max throughput

read -p 'Press any key to get current provisioned Table throughput'

az cosmosdb cassandra table throughput show \
    -a $accountName \
    -g $resourceGroupName \
    -k $keySpaceName \
    -n $tableName \
    --query resource.throughput \
    -o tsv

read -p 'Press any key to get minimum allowable Table throughput'

minimumThroughput=$(az cosmosdb cassandra table throughput show \
    -a $accountName \
    -g $resourceGroupName \
    -k $keySpaceName \
    -n $tableName \
    --query resource.minimumThroughput \
    -o tsv)

echo $minimumThroughput

# Make sure the updated throughput is not less than the minimum allowed throughput
if [ $updateThroughput -lt $minimumThroughput ]; then
    updateThroughput=$minimumThroughput
fi

read -p 'Press any key to update Table throughput'

az cosmosdb cassandra table throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -k $keySpaceName \
    -n $tableName \
    --throughput $updateThroughput

read -p 'Press any key to migrate the table from standard (manual) throughput to autoscale throughput'

az cosmosdb cassandra table throughput migrate \
    -a $accountName \
    -g $resourceGroupName \
    -k $keySpaceName \
    -n $tableName \
    -t 'autoscale'

read -p 'Press any key to read current autoscale provisioned max throughput on the table'

az cosmosdb cassandra table throughput show \
    -g $resourceGroupName \
    -a $accountName \
    -k $keySpaceName \
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
| [az cosmosdb cassandra keyspace create](https://docs.azure.cn/cli/cosmosdb/cassandra/keyspace#az_cosmosdb_cassandra_keyspace_create) | 创建 Azure Cosmos Cassandra 密钥空间。 |
| [az cosmosdb cassandra table create](https://docs.azure.cn/cli/cosmosdb/cassandra/table#az_cosmosdb_cassandra_table_create) | 创建 Azure Cosmos Cassandra 表。 |
| [az cosmosdb cassandra keyspace throughput update](https://docs.azure.cn/cli/cosmosdb/cassandra/keyspace/throughput#az_cosmosdb_cassandra_keyspace_throughput_update) | 更新 Azure Cosmos Cassandra 密钥空间的 RU/秒。 |
| [az cosmosdb cassandra table throughput update](https://docs.azure.cn/cli/cosmosdb/cassandra/table/throughput#az_cosmosdb_cassandra_table_throughput_update) | 更新 Azure Cosmos Cassandra 表的 RU/秒。 |
| [az cosmosdb cassandra 密钥空间吞吐量迁移](https://docs.azure.cn/cli/cosmosdb/cassandra/keyspace/throughput#az_cosmosdb_cassandra_keyspace_throughput_migrate) | 迁移 Azure Cosmos Cassandra 密钥空间的吞吐量。 |
| [az cosmosdb cassandra 表吞吐量迁移](https://docs.azure.cn/cli/cosmosdb/cassandra/table/throughput#az_cosmosdb_cassandra_table_throughput_migrate) | 迁移 Azure Cosmos Cassandra 表的吞吐量。 |
| [az group delete](https://docs.azure.cn/cli/group#az_group_delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure Cosmos DB CLI 的详细信息，请参阅 [Azure Cosmos DB CLI 文档](https://docs.azure.cn/cli/cosmosdb)。

可以在 [Azure Cosmos DB CLI GitHub 存储库](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)中找到所有 Azure Cosmos DB CLI 脚本示例。

<!-- Update_Description: update meta properties, wording update, update link -->