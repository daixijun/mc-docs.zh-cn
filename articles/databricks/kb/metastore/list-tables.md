---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 列出表名称 - Azure Databricks
description: 了解如何在 Azure Databricks 中列出表名称。
ms.openlocfilehash: 5a5580f77a73f6ba1c872f9f5638400a64962577
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589864"
---
# <a name="listing-table-names"></a>列出表名称

本文阐释 `spark.catalog.listTables()` 和 `%sql show tables` 为何具有不同的性能特征。

## <a name="problem"></a>问题

若要从元存储中提取所有表名称，可使用 `spark.catalog.listTables()` 或 `%sql show tables`。
如果观察提取详细信息所耗时长，会发现 `spark.catalog.listTables()` 通常需要比 `%sql show tables` 耗时更长。

## <a name="cause"></a>原因

`spark.catalog.listTables()` 首先尝试提取每个表的元数据，然后显示请求的表名称。 处理复杂的架构和更多的表时，此过程很慢。

## <a name="solution"></a>解决方案

若要仅获取表名称，请使用 `%sql show tables`，它在内部调用只提取表名称的 `SessionCatalog.listTables`。