---
title: 升级 Azure Cosmos DB API for MongoDB 帐户的 Mongo 版本
description: 如何无缝升级现有 Azure Cosmos DB API for MongoDB 帐户的 MongoDB 有线协议版本
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: guide
author: rockboyfor
ms.date: 11/09/2020
ms.testscope: yes|no
ms.testdate: 11/09/2020null
ms.author: v-yeche
ms.openlocfilehash: 97e38b89a93a549eaaf81dd983e0072f381080d2
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329426"
---
<!--Pending on PM Review for verification-->
# <a name="upgrade-the-mongodb-wire-protocol-version-of-your-azure-cosmos-dbs-api-for-mongodb-account"></a>升级 Azure Cosmos DB API for MongoDB 帐户的 MongoDB 有线协议版本
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

本文介绍如何升级 Azure Cosmos DB API for MongoDB 帐户的有线协议版本。 升级有线协议版本后，可以使用 Azure Cosmos DB API for MongoDB 的最新功能。 升级过程不会中断帐户的可用性，也不会在任何时候消耗 RU/s 或减少数据库的容量。 此过程不会影响现有数据或索引。

>[!Note]
> 目前，只有使用服务器版本 3.2 的符合条件的帐户可以升级到版本 3.6。 如果帐户未显示升级选项，请[提交支持工单](https://support.azure.cn/support/support-azure/)。

## <a name="upgrading-from-version-32-to-36"></a>从版本 3.2 升级到版本 3.6

### <a name="benefits-of-upgrading-to-version-36"></a>升级到版本 3.6 的优点

以下是版本 3.6 中包含的新功能：
- 增强性能和稳定性
- 支持新的数据库命令
- 支持聚合管道（默认）和新的聚合阶段
- 支持更改源
- 支持复合索引
- 对以下操作的跨分区支持：更新、删除、计数和排序
- 提高了以下聚合操作的性能：$count、$skip、$limit 和 $group
- 现在支持通配符索引

### <a name="changes-from-version-32"></a>自版本 3.2 以来的更改

- **删除了 RequestRateIsLarge 错误**。 来自客户端应用程序的请求将不再返回 16500 错误。 请求将继续，直到完成或超时结束。
- 每个请求超时设置为 60 秒。
- 默认情况下，在新的有线协议版本上创建的 MongoDB 集合将仅索引 `_id` 属性。

### <a name="action-required"></a>必需的操作

升级到版本 3.6 后，数据库帐户终结点后缀将更新为以下格式：

```
<your_database_account_name>.mongo.cosmos.azure.cn
```

你需要替换与此数据库帐户连接的应用程序和驱动程序中的现有终结点。 **只有使用新终结点的连接才能访问 MongoDB 版本 3.6 中的功能**。 上一个终结点的后缀应为 `.documents.azure.cn`。

<!--Not Available on This endpoint might have slight differences if your account was created in a Sovereign, Government or Restricted Azure Cloud.-->

### <a name="how-to-upgrade"></a>如何升级

1. 首先，转到 Azure 门户，导航到 Azure Cosmos DB API for MongoDB 帐户概述边栏选项卡。 验证服务器版本是否为 `3.2`。 

    :::image type="content" source="./media/mongodb-version-upgrade/1.png" alt-text="包含 MongoDB 帐户概述的 Azure 门户" border="false":::

2. 从左侧的选项中，选择 `Features` 边栏选项卡。 这会显示可用于数据库帐户的帐户级别功能。

    :::image type="content" source="./media/mongodb-version-upgrade/2.png" alt-text="包含 MongoDB 帐户概述的 Azure 门户，其中突出显示了“功能”边栏选项卡" border="false":::

3. 单击 `Upgrade to Mongo server version 3.6` 行。 如果没有看到此选项，则表示你的帐户可能没有进行此升级的资格。 如果是这种情况，请提交[支持工单](https://support.azure.cn/support/support-azure/)。

    :::image type="content" source="./media/mongodb-version-upgrade/3.png" alt-text="包含选项的“功能”边栏选项卡。" border="false":::

4. 查看显示的有关此特定升级的信息。 请注意，只有在应用程序使用更新的终结点后，此升级才会完成，正如本部分中所强调的。 如果已准备好开始该过程，请单击 `Enable`。

    :::image type="content" source="./media/mongodb-version-upgrade/4.png" alt-text="扩展的升级指南。" border="false":::

5. 开始该过程后，`Features` 菜单将显示升级状态。 状态将从 `Pending` 变为 `In Progress` 再变为 `Upgraded`。 此过程不会影响数据库帐户的现有功能或操作。

    :::image type="content" source="./media/mongodb-version-upgrade/5.png" alt-text="启动后的升级状态。" border="false":::

6. 升级完成后，状态将显示为 `Upgraded`。 单击它可以详细了解完成该过程所需执行的后续步骤和操作。 如果处理请求时出现问题，请[联系支持人员](https://support.azure.cn/support/support-azure/)。

    :::image type="content" source="./media/mongodb-version-upgrade/6.png" alt-text="已升级的帐户状态。" border="false":::

7. 若要开始使用数据库帐户升级后的版本，请返回到 `Overview` 边栏选项卡，并复制要在应用程序中使用的新的连接字符串。 应用程序连接到新终结点后，将立即开始使用升级后的版本。 现有连接不会中断，用户可以在方便时对其进行更新。 为确保获得一致的体验，所有应用程序都必须使用新的终结点。

    :::image type="content" source="./media/mongodb-version-upgrade/7.png" alt-text="新的“概述”边栏选项卡。" border="false":::

## <a name="next-steps"></a>后续步骤

- 了解 [MongoDB 版本 3.6 支持和不支持的功能](mongodb-feature-support-36.md)。
- 有关详细信息，请查看 [Mongo 3.6 版本功能](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)

<!-- Update_Description: new article about mongodb version upgrade -->
<!--NEW.date: 11/09/2020-->