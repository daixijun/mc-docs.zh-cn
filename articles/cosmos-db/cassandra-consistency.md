---
title: Apache Cassandra 和 Azure Cosmos DB 一致性级别
description: Apache Cassandra 和 Azure Cosmos DB 一致性级别。
ms.service: cosmos-db
ms.topic: conceptual
origin.date: 10/12/2020
author: rockboyfor
ms.date: 11/09/2020
ms.testscope: yes|no
ms.testdate: 11/09/2020null
ms.author: v-yeche
ms.reviewer: sngun
ms.openlocfilehash: 79ac2b0a994b87029444daeeb6ce1d8f946091d4
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329029"
---
<!--Verified successfully-->
# <a name="apache-cassandra-and-azure-cosmos-db-consistency-levels"></a>Apache Cassandra 和 Azure Cosmos DB 一致性级别
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

与 Azure Cosmos DB 不同，Apache Cassandra 本身并不提供精确定义的一致性保证。 与之相反，Apache Cassandra 提供一个写入一致性级别和一个读取一致性级别，以便进行高可用性、一致性和延迟方面的权衡。 使用 Azure Cosmos DB 的 Cassandra API 时：

* Apache Cassandra 的写入一致性级别映射到在 Azure Cosmos 帐户上配置的默认一致性级别。 写入操作 (CL) 的一致性不能因请求的不同而（即按请求）更改。

* Azure Cosmos DB 会将 Cassandra 客户端驱动程序指定的读取一致性级别动态映射到根据读取请求动态配置的某个 Azure Cosmos DB 一致性级别。

## <a name="mapping-consistency-levels"></a><a name="mapping-consistency-levels"></a>映射一致性级别

下表演示了在使用 Cassandra API 时，如何将本机 Cassandra 一致性级别映射到 Azure Cosmos DB 的一致性级别：  

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" alt-text="Cassandra 一致性模型映射" lightbox="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" :::

如果使用非常一致性以外的一致性级别配置了 Azure Cosmos 帐户，则可以通过查看概率有限过期 (PBS) 指标，找到客户端获得工作负荷的非常一致读取的概率。 此指标在 Azure 门户中公开，若要了解详细信息，请参阅[监视概率有限过期性 (PBS) 指标](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)。

概率有限过期表明了最终一致的最终程度。 通过此指标可深入了解在 Azure Cosmos 帐户中目前配置的一致性级别之间获得更非常一致性的频率。 换句话说，可看到获得写入和读取区域组合的非常一致读取的概率（以毫秒计量）。

## <a name="next-steps"></a>后续步骤

详细了解 Azure Cosmos DB 的多区域分发和一致性级别：

* [多区域分布概述](distribute-data-globally.md)
* [一致性级别概述](consistency-levels.md)
* 高可用性

<!-- Update_Description: new article about cassandra consistency -->
<!--NEW.date: 11/09/2020-->