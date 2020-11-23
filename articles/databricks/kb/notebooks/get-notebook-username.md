---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
ms.date: 06/08/2020
title: 检索笔记本的当前用户名 - Azure Databricks
description: 在运行 Azure Databricks 笔记本时识别当前用户
category: Notebook
author: bilalaslamseattle
db-author: muhammadbilal.aslam@databricks.com
ms.openlocfilehash: 603377bbb02fdabf4c58d397d47ec85ecc0e39cc
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589846"
---
# <a name="retrieve-the-current-username-for-the-notebook"></a>检索笔记本的当前用户名

> [!NOTE]
>
> Azure Databricks 不支持这些 API 调用，可以随时更改或删除它们。

## <a name="problem"></a>问题

正在运行笔记本，但不知道当前用户名。

## <a name="solution"></a>解决方案

运行交互式笔记本时，可以使用 `dbutils.notebook.getContext.tags` 获取当前用户名。

```scala
dbutils.notebook.getContext.tags("user")
```

```python
dbutils.notebook.entry_point.getDbutils().notebook().getContext().tags().apply('user')
```

将笔记本作为作业运行时，不能直接使用 `dbutils.notebook.getContext.tags`。 应改为使用笔记本小组件，将用户名作为作业参数显式传递，并从单元格访问小组件的值。

```python
dbutils.widgets.get("job_user")
```