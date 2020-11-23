---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 04/08/2020
title: 如何创建要导入到外部元存储中的表 DDL - Azure Databricks
description: 了解如何将所有表元数据从 Hive 导出到 Azure Databricks 中的外部元存储。
ms.openlocfilehash: 92df879dfaf980d28cc76a21d7fdc559da5da9dd
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589884"
---
# <a name="how-to-create-table-ddls-to-import-into-an-external-metastore"></a>如何创建要导入到外部元存储中的表 DDL

Azure Databricks 支持使用外部元存储，而不是默认的 Hive 元存储。

可以将所有表元数据从 Hive 导出到外部元存储。

1. 使用 Apache Spark `Catalog` API 列出元存储中包含的数据库中的表。
2. 使用 `SHOW CREATE TABLE` 语句生成 DDL 并将其存储到文件。
3. 使用该文件将表 DDL 导入到外部元存储。

以下代码完成前两个步骤。

```python
dbs = spark.catalog.listDatabases()
for db in dbs:
  f = open("your_file_name_{}.ddl".format(db.name), "w")
  tables = spark.catalog.listTables(db.name)
  for t in tables:
    DDL = spark.sql("SHOW CREATE TABLE {}.{}".format(db.name, t.name))
    f.write(DDL.first()[0])
    f.write("\n")
f.close()
```

可以使用生成的文件将表 DDL 导入到外部元存储。