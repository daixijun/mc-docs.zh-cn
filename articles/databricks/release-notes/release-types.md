---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/04/2020
title: Azure Databricks 预览版 - Azure Databricks
description: 了解 Azure Databricks 预览版。
ms.openlocfilehash: 78fa0f9a661e6e7a8dd439a5c33f146db7b6a122
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329019"
---
# <a name="azure-databricks-preview-releases"></a>Azure Databricks 预览版

Azure Databricks 定期提供预览版，让你有机会在功能正式发布 (GA) 之前对其进行评估并提供反馈。 这些预览版可以有不同的成熟度，本文分别定义了每个成熟度。

## <a name="platform-preview-releases"></a><a id="platform-preview-releases"> </a><a id="platform-releases"> </a>平台预览版

Azure Databricks 平台功能可以在以下任意预览级别发布：

| 版本类型                    | 谁可以使用？     | 成熟度     | 在生产环境中使用 | 接口稳定 | SLA     | 支持              | 说明                                                                                                                                                                 |
|---------------------------------|------------------|--------------|-------------------|------------------|---------|----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 个人预览版                 | 仅邀请      | 低到中等      | 否                | 否               | 否      | 工程团队     | 公共 Azure Databricks 文档中通常不介绍功能。                                                                                     |
| 公共预览版                  | 所有人         | 中到高等     | 是               | 是              | 是     | 支持团队         | 功能在公共 Azure Databricks 文档中介绍。 公共预览版功能稳定，旨在升级到正式版。                               |
| 有限的可用性 (LA)       | 仅邀请      | 高         | 是               | 是              | 是     | 支持团队         | LA 功能很罕见，它为有限的一组客户提供 GA 级别功能。 公共 Azure Databricks 文档中不介绍这些功能。 |

## <a name="databricks-runtime-preview-releases"></a><a id="databricks-runtime-preview-releases"> </a><a id="runtime-releases"> </a>Databricks Runtime 预览版

Databricks Runtime 的预览版始终标记为 Beta 版本。

| 版本类型                    | 谁可以使用？     | 成熟度     | 在生产环境中使用 | 接口稳定     | SLA     | 支持              |
|---------------------------------|------------------|--------------|-------------------|----------------------|---------|----------------------|
| Beta                            | 所有人         | 低到中等      | 否                | 否                   | 否      | 工程团队     |

Databricks Runtime 中包含的功能可在以下任意预览级别发布：

| 版本类型                    | 谁可以使用？     | 成熟度     | 在生产环境中使用 | 接口稳定 | SLA     | 支持              | 说明                                                                                                                                                           |
|---------------------------------|------------------|--------------|-------------------|------------------|---------|----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 个人预览版                 | 仅邀请      | 低到中等      | 否                | 否               | 否      | 工程团队     | 公共 Azure Databricks 文档中通常不介绍功能。 API 可能会更改。                                                           |
| 实验                    | 所有人         | 低到中等      | 否                | 否               | 否      | 工程团队     | 功能在公共 Azure Databricks 文档中介绍。 API 可能会更改。                                                                       |
| 公共预览版                  | 所有人         | 中到高等     | 是               | 是              | 是     | 支持团队         | 功能在公共 Azure Databricks 文档中介绍。 无论是在公开预览期间还是在功能正式发布后，API 都不会更改。 |

有关 Databricks Runtime 版本（包括支持生命周期和长期支持 (LTS)）的详细信息，请参阅 [Databricks Runtime 支持生命周期](runtime/databricks-runtime-ver.md)。