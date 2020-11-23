---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 无法读取 WASB 文件系统中的文件以及列出该文件系统中的目录 - Azure Databricks
description: 了解如何解释访问 Azure Databricks 中的 WASB 追加 blob 类型时发生的错误。
ms.openlocfilehash: 0c092caf014e9c7bd0e27933bfd229f865bc99b8
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589618"
---
# <a name="unable-to-read-files-and-list-directories-in-a-wasb-filesystem"></a>无法读取 WASB 文件系统中的文件以及列出该文件系统中的目录

## <a name="problem"></a>问题

尝试使用 Spark 读取 WASB 上的文件时，会出现以下异常：

```console
org.apache.spark.SparkException: Job aborted due to stage failure: Task 0 in stage 1.0 failed 4 times, most recent failure: Lost task 0.3 in stage 1.0 (TID 19, 10.139.64.5, executor 0): shaded.databricks.org.apache.hadoop.fs.azure.AzureException: com.microsoft.azure.storage.StorageException: Incorrect Blob type, please use the correct Blob type to access a blob on the server. Expected BLOCK_BLOB, actual APPEND_BLOB.
```

尝试使用 `dbutils.fs.ls` 或 Hadoop API 列出 WASB 中的文件时，会出现以下异常：

```console
java.io.FileNotFoundException: File/<some-directory> does not exist.
```

## <a name="cause"></a>原因

WASB 文件系统支持三种类型的 blob：块、页面和追加。

* 块 blob 针对大数据块（Hadoop 中的默认值）上传进行了优化。
* 页 blob 针对随机读取和写入操作进行了优化。
* 追加 Blob 针对追加操作进行了优化。

请参阅[了解块 blob、追加 blob 和页 blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) 获取详细信息。

如果尝试读取追加 blob 或列出仅包含追加 blob 的目录，将发生上述错误。 Azure Databricks 和 [Hadoop Azure](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) WASB 实现不支持读取追加 blob。 同样，在列出目录时，将忽略追加 blob。

没有解决方法可以实现读取追加 blob 或列出仅包含追加 blob 的目录。 但可以使用适用于 Python 的 Azure CLI 或 Azure 存储 SDK 来标识目录是否包含追加 blob 或文件是否是追加 blob。

可以通过运行以下 Azure CLI 命令来验证目录是否包含追加 blob：

```powershell
az storage blob list \
  --auth-mode key \
  --account-name <account-name> \
  --container-name <container-name> \
  --prefix <path>
```

结果以 JSON 文档形式返回，可以在其中轻松查找每个文件的 blob 类型。

如果目录很大，可以使用标志 `--num-results <num>` 限制结果数。

还可以使用适用于 Python 的 Azure 存储 SDK 列出和浏览 WASB 文件系统中的文件：

```python
iter = service.list_blobs("container")
for blob in iter:
  if blob.properties.blob_type == "AppendBlob":
    print("\t Blob name: %s, %s" % (blob.name, blob.properties.blob_type))
```

Azure Databricks 确实支持使用 Hadoop API 访问追加 blob，但仅在附加到文件的情况下。

## <a name="solution"></a>解决方案

没有针对此问题的解决方法。

使用适用于 Python 的 Azure CLI 或 Azure 存储 SDK 来标识目录是否包含追加 blob 或项目是追加 blob。

可以使用 RDD API 来实现 Spark SQL UDF 或自定义函数，以便使用适用于 Python 的 Azure 存储 SDK 加载、读取或转换 blob。