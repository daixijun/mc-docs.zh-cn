---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
ms.date: 08/06/2020
title: MLflow 项目无法访问 Apache Hive 表 - Azure Databricks
description: 解决当 MLflow 项目无法访问 Apache Hive 表时出现的“找不到表或视图”错误。
category: Machine Learning
author: DbVikas
db-author: vikas.yadav@databricks.com
ms.openlocfilehash: 3406bf8bdc4facca2896fa9faeb05d420a4a8a6e
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589659"
---
# <a name="mlflow-project-fails-to-access-an-apache-hive-table"></a>MLflow 项目无法访问 Apache Hive 表

## <a name="problem"></a>问题

你的某个 MLflow 项目无法访问 Hive 表并返回 `Table or view not found` 错误。

```console
pyspark.sql.utils.AnalysisException: "Table or view not found: `default`.`tab1`; line 1 pos 21;\n'Aggregate [unresolvedalias(count(1), None)]\n+- 'UnresolvedRelation `default`.`tab1`\n"
xxxxx ERROR mlflow.cli: === Run (ID 'xxxxx') failed ===
```

## <a name="cause"></a>原因

如果没有 Hive 支持，却在 MLflow 项目中创建 `SparkSession` 对象，则会发生这种情况。

## <a name="solution"></a>解决方案

在会话生成器中用 `.enableHiveSupport()` 选项配置 `SparkSession`。 在 MLflow 项目中执行此操作。

```scala
val spark = SparkSession.builder.enableHiveSupport().getOrCreate()
```