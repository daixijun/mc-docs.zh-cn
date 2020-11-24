---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: SQL 语句出错 - AnalysisException - 找不到表或视图 - Azure Databricks
description: 了解如何解决 AnalysisException SQL 错误“找不到表或视图”。
ms.openlocfilehash: c60b40fbac25cb309d8c5083045eed1563e4defb
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589501"
---
# <a name="error-in-sql-statement-analysisexception-table-or-view-not-found"></a>SQL 语句出错：`AnalysisException: Table or view not found`

## <a name="problem"></a>问题

当你尝试查询表或视图时，会收到以下错误：

```console
AnalysisException:Table or view not found when trying to query a global temp view
```

## <a name="cause"></a>原因

通常，你会创建全局 `temp` 视图，以便可以从不同的会话访问它们并在应用程序结束之前使其保持活动状态。 可以使用以下语句创建全局 `temp` 视图：

```scala
df.createOrReplaceGlobalTempView("<global-view-name>")
```

在这里，`df` 为 `DataFrame`。 创建此视图的另一种方法是使用以下语句：

```sql
CREATE GLOBAL TEMP VIEW <global-view-name>
```

将所有全局临时视图都绑定到名为 `global_temp` 的系统临时数据库。 如果在未显式提及 `global_temp` 数据库的情况下查询全局表或视图，则会发生此错误。

## <a name="solution"></a>解决方案

请始终对 `global_temp` 数据库使用限定的表名，以便可以成功查询全局视图数据。

例如：

```sql
%sql
select * from global_temp.<global-view-name>;
```