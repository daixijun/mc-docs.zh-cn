---
title: 选择一个定价层
titleSuffix: Azure Cognitive Search
description: 可在以下层中预配 Azure 认知搜索：“免费”、“基本”和“标准”，其中“标准”在各种资源配置和容量级别中均可用。
manager: nitinme
author: HeidiSteen
ms.author: v-tawe
ms.service: cognitive-search
ms.topic: conceptual
origin.date: 12/15/2020
ms.date: 01/14/2021
ms.custom: contperf-fy21q2
ms.openlocfilehash: e828553928781773f6135d4301e5f25fdcf00194
ms.sourcegitcommit: 01cd9148f4a59f2be4352612b0705f9a1917a774
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98194764"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>选择 Azure 认知搜索的定价层

[创建搜索服务](search-create-service-portal.md)时，你将选择一个定价层，此定价层在该服务的整个生存期内固定不变。 你选择的层级决定了以下事项：

+ 可创建的索引和其他对象的数量（最大限制）
+ 分区（物理存储）的大小和速度
+ 按计费费率计费，费用每月固定，但在添加分区或副本的情况下费用会增加

此外，多个[高级功能](#premium-features)对层级有要求。

## <a name="tier-descriptions"></a>层级说明

层级包括“免费”、“基本”、“标准”和“存储优化”。    “标准”和“存储优化”提供多种配置和容量。

以下 Azure 门户屏幕截图显示了可用的层，其中不包括定价层（可在门户中和[定价页](https://www.azure.cn/pricing/details/search/)上找到该层）。 

![Azure 认知搜索的定价层](media/search-sku-tier/tiers.png "Azure 认知搜索的定价层")

“免费”层可用于为较小的项目（例如，运行教程和代码示例）创建有限的搜索服务。 在内部，副本和分区可被多个订阅者共享。 不能缩放免费服务，也不能运行繁重的工作负荷。

“基本”和“标准”层是最常用的计费层，其中“标准”层是默认的层。   使用受你控制的专用资源，你可以部署较大的项目，优化性能并增大容量。

某些层已针对特定类型的工作进行优化。 例如，“标准 3 高密度(S3 HD)”是 S3 的托管模式，其中的底层硬件已针对大量的较小索引进行优化，适用于多租户方案。 S3 HD 的每单位费用与 S3 相同，但硬件经过优化，可基于大量的小型索引快速读取文件。

与“标准”层相比，“存储优化”层以更低的每 TB 价格提供更大的存储容量。 主要弊端是查询延迟更高，应根据具体的应用程序要求确认这种延迟。 若要详细了解此层的性能注意事项，请参阅[性能和优化注意事项](search-performance-optimization.md)。

预配服务时，可以在[定价页](https://www.azure.cn/pricing/details/search/)、[Azure 认知搜索中的服务限制](search-limits-quotas-capacity.md)以及门户页上找到有关各个层的详细信息。

<a name="premium-features"></a>

## <a name="feature-availability-by-tier"></a>按层划分的功能可用性

大多数功能在所有层级（包括免费层）都可用。 在少数情况下，你选择的层级会影响你实现功能的能力。 下表介绍了与服务层级相关的功能约束。

| 功能 | 限制 |
|---------|-------------|
| [索引器](search-indexer-overview.md) | 索引器在 S3 HD 上不可用。  |
| [AI 扩充](search-security-manage-encryption-keys.md) | 在免费层上运行，但不建议这样做。 |
| [客户托管的加密密钥](search-security-manage-encryption-keys.md) | 在免费层上不可用。 |
| [IP 防火墙访问](service-configure-firewall.md) | 在免费层上不可用。 |

资源密集型功能可能无法正常运行，除非你为其提供足够的容量。 例如，[AI 扩充](cognitive-search-concept-intro.md)包含长时间运行的技能，除非数据集较小，否则这些技能在免费服务中会超时。

## <a name="billable-events"></a>计费事件

基于 Azure 认知搜索构建的解决方案可能会在以下方面产生成本：

+ [服务本身的成本](#service-costs)，在全天候运行且使用最低配置（一个分区和副本）的情况下，按基准费率计算。 可以将此视为运行服务的固定成本。

+ 增加容量（副本或分区）带来的成本，该成本随计费费率的递增而增大

+ 带宽费用（出站数据传输）

+ 特定功能或特性所需的附加服务：

  + AI 扩充（需要[认知服务](https://www.azure.cn/pricing/details/cognitive-services/)）
  + 知识存储（需要 [Azure 存储](https://www.azure.cn/pricing/details/storage/)）
  + 增量扩充（需要 [Azure 存储](https://www.azure.cn/pricing/details/storage/)，适用于 AI 扩充）
  + 客户管理的密钥和双加密（需要 [Azure Key Vault](https://www.azure.cn/pricing/details/key-vault/)）

<!--   + private endpoints for a no-internet access model (requires [Azure Private Link](https://www.azure.cn/pricing/details/private-link/)) -->

### <a name="service-costs"></a>服务成本

与可以“暂停”以避免产生费用的虚拟机或其他资源不同，Azure 认知搜索服务在专门供你使用的硬件上始终可用。 因此，创建服务是一个计费事件，该事件在创建该服务时开始，在删除该服务时结束。 

最低费用是采用计费费率的第一个搜索单位（1 个副本 x 1 个分区）。 在服务的整个生命周期内，此最低费用都是固定的，因为服务不能在低于此配置的组件上运行。 超出最低费用时，可以单独添加副本和分区。 通过副本和分区递增容量会增大费用，其计算公式如下：[(副本数 x 分区数 x 费率)](#search-units)，其中，收费费率取决于所选的定价层。

在估算搜索解决方案的费用时，请记住，定价和容量不是线性的。 （容量翻倍不是支付两倍的费用，而是要支付更高的费用）有关该公式的工作方式示例，请参阅[如何分配副本和分区](search-capacity-planning.md#how-to-allocate-replicas-and-partitions)。

### <a name="bandwidth-charges"></a>带宽费用

如果 Azure 数据源与 Azure 认知搜索位于不同的区域中，则使用[索引器](search-indexer-overview.md)可能会影响计费。 在这种情况下，这是将出站数据从 Azure 数据源移动到 Azure 认知搜索的成本。 

如果在数据所在的同一区域中创建 Azure 认知搜索服务，则可以完全消除数据流出费用。 下面是摘自[带宽定价页](https://www.azure.cn/pricing/details/bandwidth/)中的一些信息：

+ Microsoft 不会对 Azure 上的任何服务的入站数据收费，
+ 也不会对 Azure 认知搜索的出站数据收费。 例如，如果你的搜索服务位于中国东部，而 Azure Web 应用位于美国东部，则 Microsoft 不会对来自中国东部的查询响应有效负载收费。
+ 在多服务解决方案中，如果所有服务都位于同一个区域，则不会对通过网络传输的数据收费。

如果服务在不同的区域中，则会针对出站数据收费。 这些费用实际上不是 Azure 认知搜索帐单的一部分。 此处之所以提到这些费用，是因为如果你使用数据或 AI 扩充索引器从不同的区域提取数据，将会在总体帐单中看到这些费用。

### <a name="ai-enrichment-with-cognitive-services"></a>使用认知服务的 AI 扩充

对于 [AI 扩充](cognitive-search-concept-intro.md)，应该计划在 S0 定价层上的 Azure 认知搜索所在的同一区域中[附加一个计费的认知服务资源](cognitive-search-attach-cognitive-services.md)，用于即用即付处理。 附加认知服务不会产生固定的费用。 只需支付所需的处理费。

| 操作 | 计费影响 |
|-----------|----------------|
| 文档破解、文本提取 | 免费 |
| 文档破解、图像提取 | 根据从文档中提取的图像数计费。 在 [索引器配置](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters)中，**imageAction** 是触发图像提取的参数。 如果 **imageAction** 设置为“none”（默认值），则不收取图像提取费用。 Azure 认知搜索的[定价详细信息](https://www.azure.cn/pricing/details/search/)页上阐述了图像提取费率。|
| [内置认知技能](cognitive-search-predefined-skills.md) | 计费费率与直接使用认知服务执行任务的费率相同。 |
| 自定义技能 | 自定义技能是你提供的功能。 使用自定义技能的费用完全取决于自定义代码是否调用其他计量的服务。 |

可以利用[增量扩充（预览版）](cognitive-search-incremental-indexing-conceptual.md)功能来提供缓存，使得在未来修改技能组后只需运行必要的认知技能，从而使索引器更高效，为你节省时间和金钱。

<a name="search-units"></a>

## <a name="billing-formula-r-x-p--su"></a>计费公式 (R x P = SU)

对于 Azure 认知搜索操作，要了解的最重要计费概念是搜索单位 (SU)。 由于 Azure 认知搜索必须同时使用副本和分区进行索引编制和查询，因此无法按其中的一个进行计费。 相反，应基于两者的组合来计费。

SU 是服务使用的副本数和分区数的乘积：  **(R x P = SU)** 。

每个服务至少从 1 个 SU（1 个分区乘以 1 个副本）开始。 任何服务的最大 SU 值为 36。 可通过多种方式来实现此最大值：例如，6 个分区 x 6 个副本，或 3 个分区 x 12 个副本。 通常使用小于总容量的值（例如，3 副本、3 分区的服务按 9 个 SU 计费）。 有关有效的组合，请参阅[分区和副本组合](search-capacity-planning.md#chart)图表。

费率按每个 SU、按小时计算。 每个层的费率渐进式提高。 层越高，分区越大且速度越快，因此，每小时的总费率更高。 可以在[定价详细信息](https://www.azure.cn/pricing/details/search/)页上查看每个层的费率。

大多数客户只是联机使用一部分总容量，将剩余的容器保持预留状态。 在计费方面，支付的每小时费用取决于联机的分区和副本数量（使用 SU 公式计算）。

## <a name="next-steps"></a>后续步骤

成本管理是容量计划的有机组成部分。 下一步请继续阅读以下文章，了解有关容量估算和成本管理的指导。

> [!div class="nextstepaction"]
> [如何在 Azure 认知搜索中管理成本和估算容量](search-sku-manage-costs.md)