---
title: 为 Azure Cosmos 帐户添加区域、更改故障转移优先级、触发故障转移
description: 为 Azure Cosmos 帐户添加区域、更改故障转移优先级、触发故障转移
ms.service: cosmos-db
ms.topic: sample
origin.date: 07/29/2020
author: rockboyfor
ms.date: 12/07/2020
ms.testscope: yes
ms.testdate: 08/10/2020
ms.author: v-yeche
ms.openlocfilehash: 6386825f14e768b5fa0c7fee570e1303cbd3d867
ms.sourcegitcommit: bbe4ee95604608448cf92dec46c5bfe4b4076961
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2020
ms.locfileid: "96598695"
---
<!--Verify successfully-->
# <a name="add-regions-change-failover-priority-trigger-failover-for-an-azure-cosmos-account-using-azure-cli"></a>使用 Azure CLI 为 Azure Cosmos 帐户添加区域、更改故障转移优先级、触发故障转移
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 Azure CLI 版本 2.9.1 或更高版本。

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../../../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)]

## <a name="sample-script"></a>示例脚本

此脚本演示三个操作。

- 将区域添加到现有 Azure Cosmos 帐户。
- 更改区域故障转移优先级（适用于使用自动故障转移的帐户）
- 触发从主要区域到次要区域的手动故障转移（适用于使用手动故障转移的帐户）

> [!NOTE]
> 更改其他属性时，无法在 Cosmos 帐户上添加和删除区域操作。

> [!NOTE]
> 此示例演示了如何使用 SQL (Core) API 帐户，但这些操作在 Cosmos DB 中的所有数据库 API 中都是相同的。

```azurecli
#!/bin/bash
# Reference: az cosmosdb | https://docs.azure.cn/cli/cosmosdb
# --------------------------------------------------
#
# Region replica operations for an Azure Cosmos account
#
# Operations:
# Add regions to an existing Cosmos account
# Change regional failover priority (applies to accounts using automatic failover)
# Trigger a manual failover from primary to secondary region (applies to accounts with manual failover)

# Note: Azure Comos accounts cannot include updates to regions with changes to other properties in the same operation

# Sign in the Azure China Cloud
az cloud set -n AzureChinaCloud
az login

# Resource group and Cosmos account variables
uniqueId=$RANDOM
resourceGroupName="Group-$uniqueId"
location='chinanorth2'
accountName="cosmos-$uniqueId" #needs to be lower case

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a Cosmos DB account with default values
# Use appropriate values for --kind or --capabilities for other APIs
az cosmosdb create -n $accountName -g $resourceGroupName

read -p "Press any key to add additional regions to this account"
az cosmosdb update \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='China North 2' failoverPriority=0 isZoneRedundant=False \
    --locations regionName='China East 2' failoverPriority=1 isZoneRedundant=False \
    --locations regionName='China East' failoverPriority=2 isZoneRedundant=False

read -p "Press any key to change the failover priority"
# Make China East the next region to fail over to instea of China East 2
az cosmosdb failover-priority-change \
    -n $accountName \
    -g $resourceGroupName \
    --failover-policies 'China North 2=0' 'China East=1' 'China East 2=2' 

read -p "Press any key to trigger a manual failover by changing region 0"
# Initiate a manual failover and promote China East 2 as primary write region
az cosmosdb failover-priority-change \
    -n $accountName \
    -g $resourceGroupName \
    --failover-policies 'China East 2=0' 'China North 2=1' 'China East=2'

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
| [az cosmosdb update](https://docs.azure.cn/cli/cosmosdb#az_cosmosdb_update) | 更新 Azure Cosmos DB 帐户（添加或删除区域）。 |
| [az cosmosdb failover-priority-change](https://docs.azure.cn/cli/cosmosdb#az_cosmosdb_failover_priority_change) | 在 Azure Cosmos DB 帐户上更新故障转移优先级或触发故障转移。 |
| [az group delete](https://docs.azure.cn/cli/group#az_group_delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure Cosmos DB CLI 的详细信息，请参阅 [Azure Cosmos DB CLI 文档](https://docs.azure.cn/cli/cosmosdb)。

可以在 [Azure Cosmos DB CLI GitHub 存储库](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)中找到所有 Azure Cosmos DB CLI 脚本示例。

<!-- Update_Description: update meta properties, wording update, update link -->