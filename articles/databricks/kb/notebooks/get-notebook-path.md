---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 如何获取当前笔记本的完整路径 - Azure Databricks
description: 了解如何使用 Scala 和 Python 获取当前 Azure Databricks 笔记本的完整路径。
ms.openlocfilehash: 7c42b47e10d43a34ca5561571f07b4eae571bb5e
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589845"
---
# <a name="how-to-get-the-full-path-to-the-current-notebook"></a>如何获取当前笔记本的完整路径

本文介绍如何获取当前笔记本的完整路径。

## <a name="scala"></a>Scala

要获取 Scala 单元格中的完整路径，请在单元格中运行以下命令：

```scala
%scala
dbutils.notebook.getContext.notebookPath
```

## <a name="python"></a>Python

要使用 Python 获取完整路径，必须获取路径并将其保存到 Scala 单元格内的小组件中，并在 Python 单元格中读取它。

1. 将值保存到 Scala 单元格内的小组件中。

   ```scala
   %scala
   dbutils.widgets.text("notebook", dbutils.notebook.getContext().notebookPath.get)
   ```

2. 从 Python 单元格中访问小组件值。

   ```python
   %python
   dbutils.widgets.get("notebook")
   ```