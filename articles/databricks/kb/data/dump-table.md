---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 如何以 CSV、JSON、XML、文本或 HTML 格式转储表 - Azure Databricks
description: 了解如何以 CSV、JSON、XML、文本或 HTML 格式从 Azure Databricks 输出表。
ms.openlocfilehash: dd16346a64f344ffb0e6f863afc1fdb4a1d9d342
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589604"
---
# <a name="how-to-dump-tables-in-csv-json-xml-text-or-html-format"></a>如何以 CSV、JSON、XML、文本或 HTML 格式转储表

你希望将 Azure Databricks 中的计算结果发送到 Azure Databricks 外。
可以使用 BI 工具通过 JDBC 连接到群集，然后从 BI 工具导出结果；也可以将表保存在 DBFS 或 Blob 存储中，然后通过 REST API 复制数据。

本文介绍一种简单的控制台工具 JSpark，该工具用于在 Spark 群集上使用 JDBC 执行 SQL 查询，以 CSV、JSON、XML、文本和 HTML 格式将远程表转储到本地磁盘。 例如：

```bash
java -Dconfig.file=mycluster.conf -jar jspark.jar -q "select id, type, priority, status from tickets limit 5"
```

返回：

```
+----+--------+--------+------+
|  id|type    |priority|status|
+----+--------+--------+------+
|9120|problem |urgent  |closed|
|9121|question|normal  |hold  |
|9122|incident|normal  |closed|
|9123|question|normal  |open  |
|9124|incident|normal  |solved|
+----+--------+--------+------+
```

[JSpark GitHub 存储库](https://github.com/MaxGekk/jspark)中提供了使用说明、示例用法、源代码以及构建好的 JAR 的链接。

你可以使用实参或配置文件（例如 [mycluster](https://github.com/MaxGekk/jspark/blob/master/src/main/resources/mycluster.conf)）指定 JDBC 连接的形参。

若要检查 JDBC 连接或对其进行故障排除，请下载 fat JAR [jspark.jar](https://bintray.com/maxgekk/generic/download_file?file_path=jspark.jar)，并将其作为常规 JAR 启动。
它包括 hive-jdbc 1.2.1 和所有必需的依赖项。