---
title: 在 Azure Cosmos DB 中优化多区域部署的成本
description: 本文说明如何在 Azure Cosmos DB 中管理多区域部署的成本。
ms.service: cosmos-db
ms.topic: conceptual
origin.date: 10/23/2020
author: rockboyfor
ms.date: 01/18/2021
ms.author: v-yeche
ms.openlocfilehash: 8b9dff8c9346d9d93d22a13d9ee3084bd4dba652
ms.sourcegitcommit: c8ec440978b4acdf1dd5b7fda30866872069e005
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98231115"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中优化多区域成本
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

可随时对 Azure Cosmos 帐户添加或删除区域。 在与帐户关联的每个区域中会保留为各种 Azure Cosmos 数据库和容器配置的吞吐量。 如果每小时的预配吞吐量（即，跨所有数据库和容器为 Azure Cosmos 帐户配置的 RU/秒总和）为 `T`，并且与数据库帐户关联的 Azure 区域数是 `N`，则对于给定小时，Cosmos 帐户的总预配吞吐量等于 `T x N RU/s`。

<!--MOONCAKE: $0.008 against CNY0.051 on 100 RU/s for Azure China-->

具有单个写入区域的预配吞吐量每 100 RU/秒的成本为 0.051 元/小时，具有多个可写区域的预配吞吐量每 100 RU/秒的成本为 0.102 元/小时。 若要了解详细信息，请参阅 Azure Cosmos DB [定价页](https://www.azure.cn/pricing/details/cosmos-db/)。

<!--MOONCAKE: $0.008 against CNY0.051 on 100 RU/s for Azure China-->

## <a name="costs-for-multiple-write-regions"></a>多个写入区域的成本

在多区域写入系统中，可用于写入操作的净 RU 会增加 `N` 倍，其中 `N` 是写入区域数。 与单区域写入不同，每个区域现在都可写并且支持冲突解决。 从成本规划角度来看，若要在中国范围内执行 `M` RU/秒的写入，需要在容器或数据库级别预配 M `RUs`。 随后可以添加任意多个区域并将它们用于写入以在中国范围内执行 `M` RU 写入。

### <a name="example"></a>示例

假设在中国北部配置了一个用于单区域写入的容器，该容器预配的吞吐量为 10K RU/秒，这个月存储的数据为 0.5 TB。 假设你添加了一个具有相同存储和吞吐量的区域“中国东部”，并且你希望能够从你的应用写入到这两个区域中的容器。 新的月度总账单（假设每月 744 个小时）将如下所示：

<!--MOONCAKE: Master region(WEST US) is China North, and the other regions are China East-->

|**Item**|**使用情况（每月）**|**费率**|**每月成本**|
|----|----|----|----|
|中国北部（单个写入区域）容器的吞吐量账单 |10K RU/秒 * 24 小时 * 31 天 |每小时每 100 RU/秒 0.051 元 |3,794.4 元 |
|中国北部和中国东部这 2 个区域（多个写入区域）中容器的吞吐量帐单 |2 * 10K RU/秒 * 24 小时 * 31 天|每小时每 100 RU/秒 0.102 元 |15,177.6 元 |
|中国北部容器的存储帐单 | 0.5 TB（或 512 GB）  |2.576 元/GB | 1318.92 元 |
|2 个区域（中国北部和中国东部）的存储帐单 | 2 * 0.5 TB（或 1,024 GB） |2.576 元/GB  | 2637.83 元 |

<!--MOONCAKE: Master region(WEST US) is China North, and the other 3 regions are China East-->

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>按每个区域提高吞吐量利用率

如果利用率不足，例如有一个或多个未充分利用的读取区域，则可以采取措施来最大限度地利用读取区域中的 RU，具体方式是从读取区域使用“更改源”或将其移动到另一个次要区域（如果已过度利用）。 你将需要确保先在写入区域中优化预配吞吐量 (RU)。 除非查询非常大，否则写入操作的开销就会大于读取操作的开销，因此维持平稳的利用率可能会很困难。 总的来说，监视区域中已消耗的吞吐量，并根据需要添加或删除区域以缩放读取和写入吞吐量，确保了解对同一区域中部署的任何应用的延迟造成的影响。

## <a name="next-steps"></a>后续步骤

接下来，可通过以下文章详细了解 Azure Cosmos DB 中的成本优化：

* 详细了解[开发和测试优化](optimize-dev-test.md)
* 详细了解[了解 Azure Cosmos DB 帐单](understand-your-bill.md)
* 详细了解如何[优化吞吐量成本](optimize-cost-throughput.md)
* 详细了解如何[优化存储成本](optimize-cost-storage.md)
* 详细了解如何[优化读取和写入成本](optimize-cost-reads-writes.md)
* 详细了解如何[优化查询成本](./optimize-cost-reads-writes.md)

<!-- Update_Description: update meta properties, wording update, update link -->