---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 作业因 Azure Data Lake Storage (ADLS) CREATE 限制而失败 - Azure Databricks
description: 了解 Azure Databricks 作业因 Azure Data Lake Storage CREATE 限制而失败时要执行的操作。
ms.openlocfilehash: b05972b3008b988db347c1947b8029b8cd6f33d1
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589457"
---
# <a name="job-failure-due-to-azure-data-lake-storage-adls-create-limits"></a>作业因 Azure Data Lake Storage (ADLS) CREATE 限制而失败

## <a name="problem"></a>问题

当运行的作业涉及到在 Azure Data Lake Storage (ADLS)（Gen1 或 Gen2）中创建文件时，会出现以下异常：

```console
Caused by: java.io.IOException: CREATE failed with error 0x83090c25 (Files and folders are being created at too high a rate). [745c5836-264e-470c-9c90-c605f1c100f5] failed with error 0x83090c25 (Files and folders are being created at too high a rate). [2019-04-12T10:06:43.1117197-07:00] [ServerRequestId:745c5836-264e-470c-9c90-c605f1c100f5]
at com.microsoft.azure.datalake.store.ADLStoreClient.getRemoteException(ADLStoreClient.java:1191)
at com.microsoft.azure.datalake.store.ADLStoreClient.getExceptionFromResponse(ADLStoreClient.java:1154)
at com.microsoft.azure.datalake.store.ADLStoreClient.createFile(ADLStoreClient.java:281)
at com.databricks.adl.AdlFileSystem.create(AdlFileSystem.java:348)
at com.databricks.spark.metrics.FileSystemWithMetrics.create(FileSystemWithMetrics.scala:280)
at com.databricks.backend.daemon.data.client.DatabricksFileSystemV2$$anonfun$create$1$$anonfun$apply$10$$anonfun$apply$11.apply(DatabricksFileSystemV2.scala:483)
```

## <a name="cause"></a>原因

每个 ADLS 订阅级别都限制了每个时间单位内可创建的文件数，尽管限制值可能会根据使用的是 ADLS Gen1 还是 Gen2 而有所不同。 超出限制时，将中止创建文件，并且作业会失败。

此错误的潜在原因包括：

* 你的应用程序创建了大量小文件。
* 外部应用程序创建了大量文件。
* 订阅的当前限制过低。

## <a name="solution"></a>解决方案

如果你的应用程序或外部应用程序将要生成大量文件，你需要优化应用程序。 如果当前订阅的限制不适用于你的用例，请联系 Microsoft Azure 团队获取帮助。