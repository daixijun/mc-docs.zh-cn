---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/10/2020
title: 删除托管 Delta Lake 表的最佳做法 - Azure Databricks
description: 了解删除托管 Delta Lake 表的最佳做法。
ms.openlocfilehash: d3129e966f903d8c1db76b6e05086327728becab
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589602"
---
# <a name="best-practices-for-dropping-a-managed-delta-lake-table"></a>删除托管 Delta Lake 表的最佳做法

无论采用何种方式删除托管表，都可能需要花费很长时间，具体取决于数据大小。 尤其是，Delta Lake 托管表会包含大量事务日志形式的元数据，而且它们可能包含重复的数据文件。 如果某个 Delta 表已经使用了很长时间，它可能会累积相当大量的数据。

在 Azure Databricks 环境中，有[两种删除表的方法](/databricks/data/tables#delete-tables)：

* 在笔记本单元格中运行 `DROP TABLE`。
* 在 UI 中单击“删除”。

你可以在后台删除表而不影响工作负载，即便如此，最好还是确保在对任何表启动删除命令之前，先运行 [DELETE FROM](/databricks/spark/latest/spark-sql/language-manual/delete-from) 和 [VACUUM](/databricks/delta/quick-start#clean-up)。 这样可以确保在启动实际数据删除之前先清除元数据和文件大小。

例如，如果尝试删除 Delta 表 `events`，请在启动 `DROP TABLE` 命令之前先运行以下命令：

1. 运行 DELETE FROM：`DELETE FROM events`
2. 运行 VACUUM，其中间隔为零：`VACUUM events RETAIN 0 HOURS`

这两个步骤减少了元数据量和未提交文件的数量，否则它们会增加数据删除时间。