---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 05/19/2020
title: 启用防火墙后无法访问 Azure Data Lake Storage (ADLS) Gen1 - Azure Databricks
description: 了解如何对从启用了防火墙的 Azure Databricks 连接到 Azure Data Lake Storage Gen 1 时出现的访问问题进行故障排除。
ms.openlocfilehash: ab6c4b51ddae46692d8ea8149ce4d7cabac4115f
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589833"
---
# <a name="unable-to-access-azure-data-lake-storage-adls-gen1-when-firewall-is-enabled"></a>启用防火墙后无法访问 Azure Data Lake Storage (ADLS) Gen1

## <a name="problem"></a>问题

在 Azure 虚拟网络 (VNet) 上启用了防火墙并且尝试使用 ADLS Gen1 连接器访问 ADLS 时，它将失败并显示以下错误：

```console
328 format(target_id, ".", name), value) 329 else: 330 raise Py4JError(Py4JJavaError:
An error occurred while calling o196.parquet.: java.lang.RuntimeException:
Could not find ADLS Token at com.databricks.backend.daemon.data.client.adl.AdlCredentialContextTokenProvider$$anonfun$get Token$1.apply(AdlCredentialContextTokenProvider.scala:18)
at com.databricks.backend.daemon.data.client.adl.AdlCredentialContextTokenProvider$$anonfun$get
Token$1.apply(AdlCredentialContextTokenProvider.scala:18)
at scala.Option.getOrElse(Option.scala:121)
at com.databricks.backend.daemon.data.client.adl.AdlCredentialContextTokenProvider.getToken(AdlCredentialContextTokenProvider.scala:18)
at com.microsoft.azure.datalake.store.ADLStoreClient.getAccessToken(ADLStoreClient.java:1036)
at com.microsoft.azure.datalake.store.HttpTransport.makeSingleCall(HttpTransport.java:177)
at com.microsoft.azure.datalake.store.HttpTransport.makeCall(HttpTransport.java:91)
at com.microsoft.azure.datalake.store.Core.getFileStatus(Core.java:655)
at com.microsoft.azure.datalake.store.ADLStoreClient.getDirectoryEntry(ADLStoreClient.java:735)
at com.microsoft.azure.datalake.store.ADLStoreClient.getDirectoryEntry(ADLStoreClient.java:718)
at com.databricks.adl.AdlFileSystem.getFileStatus(AdlFileSystem.java:423)
at org.apache.hadoop.fs.FileSystem.exists(FileSystem.java:1426)
at org.apache.spark.sql.execution.datasources.InsertIntoHadoopFsRelationCommand.run(InsertIntoHadoopFsRelationCommand.scala:94)
```

## <a name="cause"></a>原因

这是 ADLS Gen1 连接器的已知问题。 不支持在启用防火墙后连接到 ADLS Gen1。

## <a name="solution"></a>解决方案

改用 [ADLS Gen2](/databricks/data/data-sources/azure/azure-datalake-gen2)。