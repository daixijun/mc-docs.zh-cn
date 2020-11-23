---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/10/2020
title: Delta Lake 写入作业由于出现 java.lang.UnsupportedOperationException 而失败 - Azure Databricks
description: 了解如何防止 Delta Lake 写入作业中出现 java.lang.UnsupportedOperationException。
ms.openlocfilehash: 65248c90a0c8a01db3cde0b21af4c81679154917
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589739"
---
# <a name="delta-lake-write-job-fails-with-javalangunsupportedoperationexception"></a>Delta Lake 写入作业失败，出现错误 `java.lang.UnsupportedOperationException`

## <a name="problem"></a>问题

Delta Lake 写入作业有时会由于出现以下异常而失败：

```console
java.lang.UnsupportedOperationException: com.databricks.backend.daemon.data.client.DBFSV1.putIfAbsent(path: Path, content: InputStream).
DBFS v1 doesn't support transactional writes from multiple clusters. Please upgrade to DBFS v2.
Or you can disable multi-cluster writes by setting 'spark.databricks.delta.multiClusterWrites.enabled' to 'false'.
If this is disabled, writes to a single table must originate from a single cluster.
```

## <a name="cause"></a>原因

仅 DBFS v2 支持 Delta Lake 多群集写入。 Azure Databricks 群集默认使用 DBFS v2。 所有 `sparkSession` 对象均使用 DBFS v2。

但是，如果应用程序使用 `FileSystem` API 并调用 `FileSystem.close()`，则文件系统客户端将回退到默认值 v1。 在这种情况下，Delta Lake 多群集写入操作将失败。

以下日志跟踪显示文件系统对象回退到了默认的 v1 版本。

```console
<date> <time> INFO DBFS: Initialized DBFS with DBFSV1 as the delegate.
```

## <a name="solution"></a>解决方案

可以通过两种方法来防止这种情况：

1. 切勿在应用程序代码内调用 `FileSystem.close()`。
   如果需要调用 `close()` API，则首先使用当前 Apache Spark 会话中的配置对象（而不是空的配置对象）实例化新的 `FileSystem` 客户端对象：

   ```scala
   val fileSystem = FileSystem.get(new java.net.URI(path), sparkSession.sessionState.newHadoopConf())
   ```

2. 此外，此代码示例可以实现相同的目标：

   ```scala
   val fileSystem = FileSystem.get(new java.net.URI(path), sc.hadoopConfiguration())
   ```