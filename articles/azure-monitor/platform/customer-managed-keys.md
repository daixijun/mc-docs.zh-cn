---
title: Azure Monitor 客户管理的密钥
description: 使用 Azure Key Vault 密钥配置客户管理的密钥 (CMK) 以加密 Log Analytics 工作区中的数据的信息和步骤。
ms.subservice: logs
ms.topic: conceptual
author: Johnnytechn
ms.author: v-johya
ms.date: 11/02/2020
ms.openlocfilehash: 2dc906cef9018eefbaf697158cf56907dd005fe3
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329037"
---
# <a name="azure-monitor-customer-managed-key"></a>Azure Monitor 客户管理的密钥 

本文介绍了为 Log Analytics 工作区配置客户管理的密钥 (CMK) 的背景信息和步骤。 配置完成后，发送到工作区的任何数据都将使用 Azure Key Vault 密钥进行加密。

建议在配置之前，查看下方的[限制和约束](#limitationsandconstraints)。

## <a name="customer-managed-key-cmk-overview"></a>客户管理的密钥 (CMK) 概述

[静态加密](../../security/fundamentals/encryption-atrest.md)是组织中常见的隐私和安全要求。  可以让 Azure 完全管理静态加密，同时可以使用各种选项严格管理加密或加密密钥。

<!--Not available in MC: hsm-->
Azure Monitor 确保使用 Microsoft 管理的密钥 (MMK) 静态加密所有数据和保存的查询。 Azure Monitor 还可以使用你自己的密钥进行加密，该密钥存储在 [Azure Key Vault](../../key-vault/general/overview.md) 中，并且存储可通过系统分配的[托管标识](../../active-directory/managed-identities-azure-resources/overview.md)身份验证对其进行访问。 该密钥 (CMK) 可以是[软件](../../key-vault/general/overview.md)。 Azure Monitor 进行加密的操作与[Azure 存储加密](../../storage/common/storage-service-encryption.md#about-azure-storage-encryption)的操作相同。

CMK 功能在专用 Log Analytics 群集中提供，通过它可以随时撤消对数据的访问并使用 [Lockbox](#customer-lockbox-preview) 控件对其进行保护。 为验证我们在你所在区域具有专用群集所需的容量，我们要求你的订阅已事先获得允许。 开始配置 CMK 之前，请使用 Microsoft 联系人获取已获得允许的订阅。

[Log Analytics 群集定价模型](./manage-cost-storage.md#log-analytics-dedicated-clusters)使用 1000 GB/天起的容量预留。

过去 14 天内引入的数据也保存在热缓存（受 SSD 支持）中，以实现高效的查询引擎操作。 此数据使用 Microsoft 密钥进行加密，而不考虑 CMK 配置，但你对 SSD 数据的控制将遵循[密钥吊销](#cmk-kek-revocation)。 我们正致力于在 2020 的下半年使用 CMK 加密 SSD 数据。

## <a name="how-cmk-works-in-azure-monitor"></a>CMK 在 Azure Monitor 中的工作原理

Azure Monitor 利用系统分配的托管标识授予对 Azure Key Vault 的访问权限。 系统分配的托管标识只能与单个 Azure 资源关联，而 Log Analytics 群集的标识在群集级别受支持，这就规定了 CMK 功能是在专用 Log Analytics 群集上提供的。 为了在多个工作区上支持 CMK，新的 Log Analytics 群集资源将用作 Key Vault 和 Log Analytics 工作区之间的中间标识连接。 Log Analytics 群集存储使用与群集资源关联的托管标识，通过 Azure Active Directory 对 Azure Key Vault 进行身份验证。 

CMK 配置完成后，与专用群集链接的工作区中引入的所有数据都将使用 Key Vault 中的密钥进行加密。 可以随时取消工作区与群集的链接。 新数据将引入到 Log Analytics 存储，并使用 Microsoft 密钥进行加密，同时你可以无缝查询新旧数据。


![CMK 概述](./media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. Log Analytics 群集资源具有拥有 Key Vault 权限的托管标识，此标识可传播到基础专用 Log Analytics 群集存储
3. 专用 Log Analytics 群集
4. 与用于 CMK 加密的群集资源链接的工作区

## <a name="encryption-keys-operation"></a>加密密钥操作

存储数据加密涉及 3 种类型的密钥：

- **KEK** - 密钥加密密钥 (CMK)
- **AEK** - 帐户加密密钥
- **DEK** - 数据加密密钥

下列规则适用：

- Log Analytics 群集存储帐户为每个存储帐户生成唯一的加密密钥，称为 AEK。

- AEK 用于派生 DEK 密钥，后者用于对写入磁盘的每个数据块进行加密。

- 在 Key Vault 中配置密钥并在群集中引用它时，Azure 存储会将请求发送到 Azure Key Vault 以包装和解包 AEK，从而执行数据加密和解密操作。

- KEK 绝不会离开 Key Vault，也绝不会离开硬件。

- Azure 存储使用与群集资源关联的托管标识通过 Azure Active Directory 对 Azure Key Vault 进行身份验证和访问。

## <a name="cmk-provisioning-procedure"></a>CMK 预配过程

1. 允许订阅 -- CMK 功能是在专用 Log Analytics 群集上提供的。 为验证我们在你所在区域具有所需的容量，我们要求你的订阅已事先获得允许。 使用 Microsoft 联系人获取已获得允许的订阅。
2. 创建 Azure Key Vault 和存储密钥
3. 创建群集
4. 向 Key Vault 授予权限
5. 链接 Log Analytics 工作区

Azure 门户中不支持 CMK 配置，并且预配是通过 [PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/)、[CLI](/cli/monitor/log-analytics) 或 [REST](https://docs.microsoft.com/rest/api/loganalytics/) 请求执行的。

### <a name="asynchronous-operations-and-status-check"></a>异步操作和状态检查

某些配置步骤是异步运行的，因为它们无法快速完成。 在配置中使用 REST 请求时，响应在接受时最初返回 HTTP 状态代码 200 (OK) 和具有 Azure-AsyncOperation 属性的标头：
```json
"Azure-AsyncOperation": "https://management.chinacloudapi.cn/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

若要查看异步操作的状态，请向 Azure-AsyncOperation 标头值发送 GET 请求：
```rst
GET https://management.chinacloudapi.cn/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

响应包含有关操作及其状态的信息。 可以是以下之一：

正在执行操作
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "InProgress", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
}
```

正在执行密钥标识符更新操作
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Updating", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

正在删除群集 - 删除具有链接工作区的群集时，将以异步方式对每个工作区执行取消链接操作，并且该操作可能需要一段时间。
这种情况与删除不具有链接工作区的群集并不相关，在后一情况中，系统将立即删除群集。
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Deleting", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

操作完成
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Succeeded", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

操作失败
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Failed", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
    "error" : { 
        "code": "error-code",  
        "message": "error-message" 
    }
}
```

### <a name="allowing-subscription-for-cmk-deployment"></a>允许用于 CMK 部署的订阅

CMK 功能是在专用 Log Analytics 群集上提供的。 为验证我们在你所在区域具有所需的容量，我们要求你的订阅已事先获得允许。 在 Microsoft 中使用联系人提供订阅 ID。

> [!IMPORTANT]
> CMK 功能是区域性的。 Azure Key Vault、群集和链接的 Log Analytics 工作区必须位于同一区域，但可以位于不同订阅。

### <a name="storing-encryption-key-kek"></a>存储加密密钥 (KEK)

创建或使用已有的 Azure Key Vault，以生成或导入用于数据加密的密钥。 必须将 Azure Key Vault 配置为可恢复，以保护密钥以及对 Azure Monitor 中的数据的访问权限。 可以验证是否应启用 Key Vault 中“软删除”和“清除保护”属性下的此配置 。

![软删除和清除保护设置](./media/customer-managed-keys/soft-purge-protection.png)

可以通过 CLI 和 PowerShell 更新这些设置：

- [软删除](../../key-vault/general/soft-delete-overview.md)
- [清除保护](../../key-vault/general/soft-delete-overview.md#purge-protection)可防止在软删除后强行删除机密/保管库

### <a name="create-cluster"></a>创建群集

请遵循[“专用群集”一文](/azure-monitor/log-query/logs-dedicated-clusters#creating-a-cluster)中说明的过程。 

> [!IMPORTANT]
> 复制并保存响应，因为你在后续步骤中需要详细信息。

### <a name="grant-key-vault-permissions"></a>授予 Key Vault 权限

使用新的访问策略更新 Key Vault，该策略会向群集授予权限。 基础 Azure Monitor 存储使用这些权限进行数据加密。 在 Azure 门户中打开 Key Vault，单击“访问策略”，然后单击“+ 添加访问策略”以使用以下设置创建策略：

- 密钥权限：选择“获取”、“包装密钥”和“解包密钥”权限。
- 选择主体：输入在上一步的响应中返回的群集名称或主体 id 值。

![授予 Key Vault 权限](./media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

需要“获取”权限，才能验证是否已将 Key Vault 配置为可恢复以保护密钥以及对 Azure Monitor 数据的访问。

### <a name="update-cluster-with-key-identifier-details"></a>为群集更新密钥标识符详细信息

对群集的所有操作均需要 Microsoft.OperationalInsights/clusters/write 操作权限。 可以通过包含 /写入操作的所有者或参与者或包含 Microsoft.OperationalInsights/ 操作的 Log Analytics 参与者角色授予此权限。

此步骤使用要用于数据加密的密钥和版本更新 Azure Monitor 存储。 更新后，新密钥将用于包装和解包到存储密钥 (AEK)。

在 Azure Key Vault 中选择密钥的当前版本，以获取密钥标识符详细信息。

![授予 Key Vault 权限](./media/customer-managed-keys/key-identifier-8bit.png)

为群集中的 KeyVaultProperties 更新密钥标识符详细信息。

该操作是异步操作，可能需要一段时间才能完成。

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -KeyVaultUri "key-uri" -KeyName "key-name" -KeyVersion "key-version"
```

> [!NOTE]
> 可以使用 PATCH 更新群集 sku、keyVaultProperties 或 billingType  。

```rst
PATCH https://management.chinacloudapi.cn/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
Authorization: Bearer <token>
Content-type: application/json

{
   "identity": { 
     "type": "systemAssigned" 
     },
   "sku": {
     "name": "capacityReservation",
     "capacity": 1000
     },
   "properties": {
    "billingType": "cluster",
     "KeyVaultProperties": {
       "KeyVaultUri": "https://<key-vault-name>.vault.azure.cn",
       "KeyName": "<key-name>",
       "KeyVersion": "<current-version>"
       }
   },
   "location":"<region-name>"
}
```

**响应**

200 OK 和标头。
完成密钥标识符的传播需要几分钟。 可以通过两种方式检查更新状态：
1. 从响应中复制 Azure-AsyncOperation URL 值，并进行[异步操作状态检查](#asynchronous-operations-and-status-check)。
2. 在群集上发送 GET 请求，然后查看 KeyVaultProperties 属性。 你最近更新的密钥标识符详细信息应返回到响应中。

密钥标识符更新完成后，对 GET 请求的响应应如下所示：

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
    },
  "sku": {
    "name": "capacityReservation",
    "capacity": 1000,
    "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
    },
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.cn",
      "kyName": "key-name",
      "keyVersion": "current-version"
      },
    "provisioningState": "Succeeded",
    "billingType": "cluster",
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

### <a name="link-workspace-to-cluster"></a>将工作区链接到群集

需要具有对工作区和群集的“写入”权限才能执行此操作，其中包括以下操作：

- 在工作区中：Microsoft.OperationalInsights/workspaces/write
- 在群集中：Microsoft.OperationalInsights/clusters/write

> [!IMPORTANT]
> 完成 Log Analytics 群集的预配后才应执行此步骤。 如果在预配前链接工作区并引入数据，则引入的数据将被删除，并且无法恢复。

此操作是异步操作，可能需要一段时间才能完成。

请遵循[“专用群集”一文](/azure-monitor/log-query/logs-dedicated-clusters#link-a-workspace-to-the-cluster)中说明的过程。

## <a name="cmk-kek-revocation"></a>CMK (KEK) 吊销

可以通过禁用密钥，或删除 Key Vault 中的群集访问策略来撤销对数据的访问。 Log Analytics 群集存储在一小时或更短时间内将始终遵循关键权限的更改，并且存储将变得不可用。 与群集链接的工作区中引入的任何新数据都将被删除，并且无法恢复，数据将不可访问，针对这些工作区的查询将会失败。 只要不删除群集和工作区，之前引入的数据就会保留在存储中。 不可访问的数据由数据保留策略管理，并在保留期截止时被清除。 

过去 14 天内引入的数据也保存在热缓存（SSD 提供支持）中，以实现高效的查询引擎操作。 它将在进行密钥吊销操作后被删除，并且也变得不可访问。

存储会定期轮询 Key Vault 以尝试解包加密密钥，数据引入和查询将在受到访问后的 30 分钟内恢复。

## <a name="cmk-kek-rotation"></a>CMK (KEK) 轮换

CMK 的轮换需要使用 Azure Key Vault 中的新密钥版本对群集进行显式更新。 按照“为群集更新密钥标识符详细信息”步骤中的说明进行操作。 如果未在群集中更新新密钥标识符详细信息，Log Analytics 群集存储将继续使用之前的密钥进行加密。 如果在更新群集中的新密钥之前禁用或删除旧密钥，则你将进入[密钥吊销](#cmk-kek-revocation)状态。

进行密钥轮换操作后，所有数据都将保持可访问，因为数据始终使用帐户加密密钥 (AEK) 进行加密，而 AEK 目前使用 Key Vault 中的新密钥加密密钥 (KEK) 版本进行加密。

## <a name="cmk-for-queries"></a>用于查询的 CMK

Log Analytics 中使用的查询语言可以实现丰富的表达，并且可以在添加到查询的注释中或查询语法中包含敏感信息。 某些组织要求将此类信息作为 CMK 策略的一部分进行保护，因此你需要保存使用密钥加密的查询。 使用 Azure Monitor 可以在连接到工作区时将采用密钥加密的已存搜索查询和日志警报查询存储到你自己的存储帐户 。 

> [!NOTE]
> 根据所使用的方案，可将 Log Analytics 查询保存到各种存储。 在以下方案中，仍使用 Microsoft 密钥 (MMK) 对查询加密，而不考虑 CMK 配置：Azure Monitor 中的工作簿、Azure 仪表板、Azure 逻辑应用、Azure Notebooks 和自动化 runbook。

自带存储 (BYOS) 并将其链接到工作区时，该服务会将已存搜索查询和日志警报查询上传到存储帐户 。 这意味着，可以使用加密 Log Analytics 群集中数据的密钥或其他密钥来控制存储帐户和静态加密策略。 但需支付与该存储帐户相关的费用。 

**为查询设置 CMK 之前的注意事项**
* 需拥有对工作区和存储帐户的“写入”权限
* 确保在 Log Analytics 工作区所在区域创建存储帐户
* 存储中的保存搜索视为服务项目，并且其格式可能会发生变化
* 从工作区删除现有的保存搜索。 复制配置之前需要的所有保存搜索。 可以使用 [PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/get-azoperationalinsightssavedsearch) 查看 *已存搜索*
* 不支持查询历史记录，因此无法查看已运行的查询
* 可以出于保存查询的目的将单个存储帐户链接到工作区，且该帐户可同时用于已存搜索查询和日志警报查询 
* 不支持“固定到仪表板”

**为已存搜索查询配置 BYOS**

将“查询”的存储帐户链接到工作区 - 已存搜索查询保存在存储帐户中 。 

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Query -StorageAccountIds $storageAccount.Id
```

```rst
PUT https://management.chinacloudapi.cn/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Query?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Query", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

完成配置后，所有新的已存搜索查询将保存在存储中。

**为日志警报查询配置 BYOS**

将“警报”的存储帐户链接到工作区 - 日志警报查询保存在存储帐户中 。 

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Alerts -StorageAccountIds $storageAccount.Id
```

```rst
PUT https://management.chinacloudapi.cn/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Alerts?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Alerts", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

完成配置后，所有新的日志警报查询将保存在存储中。

## <a name="customer-lockbox-preview"></a>客户密码箱（预览版）
通过密码箱，可以在支持请求期间批准或拒绝 Microsoft 工程师的数据访问请求。

在 Azure Monitor 中，对 Log Analytics 专用群集所链接的工作区中的数据也可以实现上述操作。 密码箱适用于 Log Analytics 专用群集中存储的数据，在群集中这些数据以隔离形式存在于受密码箱保护的订阅下的群集存储帐户中。  

## <a name="cmk-management"></a>CMK 管理

- **获取资源组中的所有群集**
  
  ```powershell
  Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name"
  ```

  ```rst
  GET https://management.chinacloudapi.cn/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **响应**
  
  ```json
  {
    "value": [
      {
        "identity": {
          "type": "SystemAssigned",
          "tenantId": "tenant-id",
          "principalId": "principal-Id"
        },
        "sku": {
          "name": "capacityReservation",
          "capacity": 1000,
          "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
          },
        "properties": {
           "keyVaultProperties": {
              "keyVaultUri": "https://key-vault-name.vault.azure.cn",
              "keyName": "key-name",
              "keyVersion": "current-version"
              },
          "provisioningState": "Succeeded",
          "billingType": "cluster",
          "clusterId": "cluster-id"
        },
        "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
        "name": "cluster-name",
        "type": "Microsoft.OperationalInsights/clusters",
        "location": "region-name"
      }
    ]
  }
  ```

- **获取订阅中的所有群集**
  
  ```powershell
  Get-AzOperationalInsightsCluster
  ```

  ```rst
  GET https://management.chinacloudapi.cn/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```
    
  **响应**
    
  与“资源组的群集”的响应相同，但在订阅范围内。

- 更新群集中的容量预留

  链接工作区的数据量随时间变化时，建议适当地更新容量预留级别。 按照[更新群集](#update-cluster-with-key-identifier-details)中的步骤进行操作，并提供新的容量值。 它可以在每天 1,000 到 3,000 GB 范围内，并以 100 为度。 如果级别高于每日 3,000 GB，请联系 Microsoft 联系人实现该级别。 请注意，无需提供完整的 REST 请求正文，但应包含 sku：

  ```powershell
  Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -SkuCapacity "daily-ingestion-gigabyte"
  ```

  ```rst
  PATCH https://management.chinacloudapi.cn/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": 1000
    }
  }
  ```

- 更新群集中的 billingType

  billingType 属性可确定群集及其数据的计费归属：
  - 群集（默认）-- 计费归属于承载群集资源的订阅
  - 工作区 -- 计费按比例归属于承载工作区的订阅
  
  按照[更新群集](#update-cluster-with-key-identifier-details)中的步骤进行操作，并提供新的 billingType 值。 请注意，无需提供完整的 REST 请求正文，但应包含 billingType：

  ```rst
  PATCH https://management.chinacloudapi.cn/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ``` 

- **取消链接工作区**

  你需要对工作区具有“写入”权限，并且需要群集才能执行此操作。 可以随时取消工作区与群集的链接。 进行解除链接操作后的新引入数据存储在 Log Analytics 存储中，并使用 Microsoft 密钥进行加密。 只要使用有效的 Key Vault 密钥预配和配置了群集，就可以在解除链接前后无缝查询工作区中引入的数据。

  此操作是异步操作，可能需要一段时间才能完成。

  ```powershell
  Remove-AzOperationalInsightsLinkedService -ResourceGroupName "resource-group-name" -Name "workspace-name" -LinkedServiceName cluster
  ```

  ```rest
  DELETE https://management.chinacloudapi.cn/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **响应**

  200 OK 和标头。

  进行解除链接操作后的引入数据存储在 Log Analytics 存储中，这需要 90 分钟才能完成。 可以通过两种方式检查工作区取消链接状态：

  1. 从响应中复制 Azure-AsyncOperation URL 值，并进行[异步操作状态检查](#asynchronous-operations-and-status-check)。
  2. 发送 [Workspaces – Get](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) 请求并观察响应，解除链接的工作区的“功能”下方将不会具有 clusterResourceId 。

- **检查工作区链接状态**
  
  对工作区执行 Get 操作，并观察功能下的响应中是否存在 clusterResourceId 属性 。 链接的工作区将具有 clusterResourceId 属性。

  ```powershell
  Get-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
  ```

- **删除群集**

  你需要对群集具有“写入”权限才能执行此操作。 如果执行的是软删除操作，则不管该删除操作是有意还是无意，均可在 14 天内恢复群集及其 14 天内的数据。 软删除期间，群集名称保持为预留，并且无法新建同名群集。 软删除结束后将释放群集名称，并将永久删除群集和数据，不可恢复。 执行删除操作后，所有的链接工作区都将与群集断开链接。 新引入数据存储在 Log Analytics 存储中，并使用 Microsoft 密钥进行加密。 
  
  解除链接操作是异步操作，可能需要长达 90 分钟才能完成。

  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

  ```rst
  DELETE https://management.chinacloudapi.cn/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **响应**

  200 正常

- **恢复群集和数据** 
  
  过去 14 天内删除的群集处于软删除状态，因此该群集资源及其数据均可恢复。 由于删除群集后所有工作区均与群集断开链接，因此你需要在恢复之后重新链接工作区以实现 CMK 加密。 当前，恢复操作由产品组手动执行。 使用 Microsoft 通道实现恢复请求。

## <a name="limitations-and-constraints"></a>限制和约束

- CMK 在专用 Log Analytics 群集上受支持，适用于每天发送 1TB 或更多的客户。

- 每个区域和每个订阅的群集的最大数目为 2

与群集链接的工作区的最大数目为 100

- 可以将工作区链接到群集，如果工作区不需要 CMK，可取消链接。 在 30 天内，工作区与特定工作区的链接数限制为 2。

- 验证是否完成 Log Analytics 群集预配之后，工作区才能与群集链接。  完成预配之前发送到工作区的数据将被删除，并且无法恢复。

- CMK 加密适用于 CMK 配置后的新引入数据。  CMK 配置之前引入的数据仍将使用 Microsoft 密钥进行加密。  你可以无缝查询 CMK 配置前后的数据。

- Azure Key Vault 必须配置为可恢复。 默认情况下，这些属性不会启用，并且应使用 CLI 或 PowerShell 对其进行配置：<br>
  - [软删除](../../key-vault/general/soft-delete-overview.md)
  - 应打开[清除保护](../../key-vault/general/soft-delete-overview.md#purge-protection)防止在软删除后强行删除机密/保管库。

- 目前不支持将群集移动到另一个资源组或订阅。

- Azure Key Vault、群集和链接的工作区必须位于同一区域和同一 Azure Active Directory (Azure AD) 租户，但可以位于不同订阅。

- 如果链接到其他群集，则将工作区链接到群集将失败

## <a name="troubleshooting"></a>疑难解答

- Key Vault 可用性的行为
  - 在正常操作中，存储会缓存 AEK 一小段时间，并返回 Key Vault 定期进行解包。
    
  - 暂时性连接错误 -- 存储通过允许密钥在缓存中保留一小段时间来处理暂时性错误（超时、连接失败、DNS 问题），这可以克服可用性方面的任何小问题。 查询和引入功能将继续运行而不会中断。
    
  - 实时网站 -- 如果在约 30 分钟内未进行访问，则会导致无法使用存储帐户。 查询功能不可用，引入的数据会使用 Microsoft 密钥缓存几个小时，以避免数据丢失。 恢复对 Key Vault 的访问后，查询将变为可用，临时缓存的数据将引入到数据存储并使用 CMK 进行加密。

  - Key Vault 访问速率 -- Azure Monitor 存储为实现包装和解包操作而访问 Key Vault 的频率介于 6 到 60 秒之间。

- 如果创建群集并立即指定 KeyVaultProperties，则操作可能会失败，因为将系统标识分配给群集后才能定义访问策略。

- 如果使用 KeyVaultProperties 更新现有的群集，并且 Key Vault 中缺少“Get”密钥访问策略，则该操作将失败。

- 如果创建群集时出现冲突错误，原因可能是你在过去 14 天内删除了群集，并且它处于软删除期间。 软删除期间，群集名称保持为预留，并且无法新建同名群集。 永久删除群集时，名称将在软删除期结束后释放。

- 如果在操作过程中更新群集，则该操作将失败。

- 如果无法部署群集，请验证 Azure Key Vault、群集和链接的 Log Analytics 工作区是否位于同一区域。 可以位于不同的订阅。

- 如果在 Key Vault 中更新密钥版本，但未更新群集中的新密钥标识符详细信息，则 Log Analytics 群集将继续使用之前的密钥，并且数据将变得不可访问。 更新群集中的新密钥标识符详细信息以恢复数据引入和数据查询功能。

- 部分操作较为耗时，可能需要一段时间才能完成 - 包括群集创建、群集密钥更新和群集删除。 可以通过两种方式检查操作状态：
  1. 使用 REST 时，从响应中复制 Azure-AsyncOperation URL 值，并进行[异步操作状态检查](#asynchronous-operations-and-status-check)。
  2. 将 GET 请求发送到群集或工作区，然后观察响应。 例如，未链接的工作区在“功能”下没有 clusterResourceId 。

- 与客户管理的密钥相关的支持和帮助，请在 Microsoft 中使用你的联系人信息。

- 错误消息
  
  群集创建：
  -  400 -- 群集名称无效。 群集名称可包含字符 a-z、A-Z、0-9，且长度为 3-63。
  -  400 -- 请求的正文为 Null 或格式错误。
  -  400 -- SKU 名称无效。 将 SKU 名称设置为 CapacityReservation。
  -  400 -- 提供了容量，但 SKU 不是 capacityReservation。 将 SKU 名称设置为 CapacityReservation。
  -  400 -- SKU 容量不足。 将“容量”值设置为 1000 或更高（以 100 (GB) 为度）。
  -  400 -- SKU 中的容量不在范围内。 应介于 1000 到最大允许容量之间，最大允许容量可在工作区中的“用量和预估成本”下找到。
  -  400 -- 容量锁定 30 天。 更新后 30 天内允许减少容量。
  -  400 -- 未设置 SKU。 将 SKU 名称设置为 CapacityReservation，将“容量”值设置为 1000 或更高（以 100 (GB) 为增加幅度）。
  -  400 -- 标识为 Null 或为空。 设置具有 systemAssigned 类型的标识。
  -  400 -- KeyVaultProperty 是创建时设置的。 创建群集后更新 KeyVaultProperties。
  -  400 -- 现在无法执行操作。 异步操作处于非成功状态。 群集必须完成其操作，才能执行任意更新操作。

  群集更新
  -  400 -- 群集处于正在删除状态。 正在执行异步操作。 群集必须完成其操作，才能执行任意更新操作。
  -  400 -- KeyVaultProperties 不为空，但格式错误。 请参阅[密钥标识符更新](#update-cluster-with-key-identifier-details)。
  -  400 -- 无法验证 Key Vault 中的密钥。 可能是由于权限不足或密钥不存在。 验证是否在 Key Vault 中[设置密钥和访问策略](#grant-key-vault-permissions)。
  -  400 -- 密钥不可恢复。 Key Vault 必须设置为“软删除”和“清除保护”。 请参阅 [Key Vault 文档](../../key-vault/general/soft-delete-overview.md)
  -  400 -- 现在无法执行操作。 等待异步操作完成，然后重试。
  -  400 -- 群集处于正在删除状态。 等待异步操作完成，然后重试。

  群集获取：
    -  404 -- 找不到群集，群集可能已删除。 如果尝试使用该名称创建群集但发生冲突，则该群集将处于软删除状态，为期 14 天。 可以联系支持人员将其恢复，也可以使用其他名称创建新群集。 

  群集删除
    -  409 -- 处于预配状态时无法删除群集。 等待异步操作完成，然后重试。

  工作区链接：
  -  404 -- 找不到工作区。 指定的工作区不存在或已被删除。
  -  409 -- 正在执行工作区链接或取消链接操作。
  -  400 -- 找不到群集，指定的群集不存在或已被删除。 如果尝试使用该名称创建群集但发生冲突，则该群集将处于软删除状态，为期 14 天。 可以联系支持人员将其恢复。

  工作区取消链接：
  -  404 -- 找不到工作区。 指定的工作区不存在或已被删除。
  -  409 -- 正在执行工作区链接或取消链接操作。

