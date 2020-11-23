---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/10/2020
title: CosmosDB-Spark 连接器库冲突 - Azure Databricks
description: 了解如何解决在将 CosmosDB-Spark 连接器库与 Azure Databricks 结合使用时出现的冲突。
ms.openlocfilehash: 16a6d75d792c07cc06cf7eb13f70dfb4a7dfd3a8
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589621"
---
# <a name="cosmosdb-spark-connector-library-conflict"></a>CosmosDB-Spark 连接器库冲突

本文介绍如何解决在 Azure Databricks 环境中运行使用 CosmosDB-Spark 连接器的应用程序时遇到的问题。

## <a name="affected-versions"></a>受影响版本

Databricks Runtime 4.0 及更高版本（包括 Spark 2.3 的运行时）。

## <a name="problem"></a>问题

通常，如果将 Maven 依赖项添加到 Spark 群集，应用应能够使用所需的连接器库。 但目前，如果只是将 CosmosDB-Spark 连接器的 Maven 坐标指定为群集的依赖项，则会遇到以下异常：

```console
java.lang.NoClassDefFoundError: Could not initialize class com.microsoft.azure.cosmosdb.Document
```

## <a name="cause"></a>原因

出现这种情况的原因是 Spark 2.3 使用 `jackson-databind-2.6.7.1`，而 CosmosDB-Spark 连接器使用 `jackson-databind-2.9.5`。 这会产生库冲突，并且在执行器级别，你会看到以下异常：

```console
java.lang.NoSuchFieldError: ALLOW_TRAILING_COMMA
at com.microsoft.azure.cosmosdb.internal.Utils.<clinit>(Utils.java:69)
```

## <a name="solution"></a>解决方案

避免此问题的方法：

1. 直接下载 CosmosDB-Spark 连接器 Uber JAR：[azure-cosmosdb-spark_2.3.0_2.11-1.2.2-uber.jar](https://repo1.maven.org/maven2/com/microsoft/azure/azure-cosmosdb-spark_2.3.0_2.11/1.2.2/azure-cosmosdb-spark_2.3.0_2.11-1.2.2-uber.jar)。
2. 按照[安装上传的库](/databricks/libraries)中的说明，将下载的 JAR 上传到 Azure Databricks。
3. [安装上传的库](/databricks/libraries)，将其安装到 Azure Databricks 群集中。

有关详细信息，请参阅 [Azure Cosmos DB](/databricks/data/data-sources/azure/cosmosdb-connector)。