---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 06/03/2020
title: Databricks 运行时支持生命周期 - Azure Databricks
keywords: 弃用, 已弃用
description: 了解 Databricks 运行时的支持策略。
ms.openlocfilehash: dbd551ab8f7bc41b2e233524366d8a913addea26
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329115"
---
# <a name="databricks-runtime-support-lifecycle"></a><a id="databricks-runtime-support-lifecycle"> </a><a id="runtime-support"> </a>Databricks 运行时支持生命周期

## <a name="support-lifecycle-for-databricks-runtime-versions"></a>Databricks Runtime 版本的支持生命周期

| 相位                               | 保证                                                                                                                                                                                                                                                                                                                                                                                                                       |
|-------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Beta                                | “支持 SLA”不适用。 有关详细信息，请参阅 [Databricks Runtime 预览版本](../release-types.md#runtime-releases)。                                                                                                                                                                                                                                                                                          |
| 完全支持                        | 主要的稳定性和安全修补程序可后向移植。<br><br>对 Databricks Runtime 版本的完全支持持续六个月，但长期支持 (LTS) 版本除外，Databricks 对该版本的支持为两年。                                                                                                                                                                                                    |
| 支持结束 (EOS)                | 该版本不受支持：<br><br>* 这些版本上运行的工作负载不会获得 Databricks 支持<br>* Databricks 不会向后移植修补程序<br><br>受支持的版本及其支持结束日期发布在[受支持的 Databricks Runtime 版本和支持计划](releases.md#supported-releases)中。<br><br>不受支持的版本发布在[不受支持的版本](releases.md#unsupported-releases)中。 |
| 生命周期结束 (EOL)                   | Databricks 在支持结束后的任何时间保留从 API 中完全删除发布版本的权利，恕不另行通知。                                                                                                                                                                                                                                                                                          |

## <a name="support-lifecycle-for-databricks-light-versions"></a><a id="light-support"> </a><a id="support-lifecycle-for-databricks-light-versions"> </a>Databricks Light 版本的支持生命周期

| 相位                               | 保证                                                                                                                                                                                                                                                                              |
|-------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Beta                                | “支持 SLA”不适用。 有关详细信息，请参阅 [Databricks Runtime 预览版本](../release-types.md#runtime-releases)。                                                                                                                                                 |
| 完全支持                        | 主要的稳定性和安全修补程序可后向移植。<br><br>所提供的完全支持的持续时间为两者中较近者：(a) 发布后的 12 个月或 (b) 下一次 Databricks Light 次要版本发布后两个月。                                                                                                 |
| 支持结束 (EOS)                | 该版本不受支持：<br><br>* 这些版本上运行的工作负载不会获得 Databricks 支持<br>* Databricks 不会向后移植修补程序<br><br>支持结束的日期为两者中较近者：(a) 发布后的 12 个月或 (b) 下一次 Databricks Light 次要版本发布后两个月。 |
| 生命周期结束 (EOL)                   | Databricks 在支持结束后的任何时间保留从 API 中完全删除发布版本的权利，恕不另行通知。                                                                                                                                                 |

> [!NOTE]
>
> 此策略中未涵盖由第三方提供的产品和服务（包括[合作伙伴数据集成](../../integrations/ingestion/index.md)）。

有关如何构造 REST API 调用的 Databricks Runtime 版本字符串的信息，请参阅 [Runtime 版本字符串](../../dev-tools/api/latest/index.md#programmatic-version)。