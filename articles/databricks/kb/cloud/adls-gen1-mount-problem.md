---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/10/2020
title: 无法装载 Azure Data Lake Storage Gen1 帐户 - Azure Databricks
description: 了解如何解决将 Azure Data Lake Storage Gen1 装载到 Azure Databricks 时出现的错误。
ms.openlocfilehash: c7397a6ea386cfe555f7f746700e153b55bce7c3
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589811"
---
# <a name="unable-to-mount-azure-data-lake-storage-gen1-account"></a>无法装载 Azure Data Lake Storage Gen1 帐户

## <a name="problem"></a>问题

尝试在 Azure Databricks 上装载 Azure Data Lake Storage (ADLS) Gen1 帐户时，该操作失败并显示以下错误：

```console
com.microsoft.azure.datalake.store.ADLException: Error creating directory /
Error fetching access token
Operation null failed with exception java.io.IOException : Server returned HTTP response code: 401 for URL: https://login.windows.net/18b0b5d6-b6eb-4f5d-964b-c03a6dfdeb22/oauth2/token
Last encountered exception thrown after 5 tries. [java.io.IOException,java.io.IOException,java.io.IOException,java.io.IOException,java.io.IOException]
 [ServerRequestId:null]
at com.microsoft.azure.datalake.store.ADLStoreClient.getExceptionFromResponse(ADLStoreClient.java:1169)
at com.microsoft.azure.datalake.store.ADLStoreClient.createDirectory(ADLStoreClient.java:589)
at com.databricks.adl.AdlFileSystem.mkdirs(AdlFileSystem.java:533)
At com.databricks.backend.daemon.data.client.DatabricksFileSystemV2$$anonfun$mkdirs$1$$anonfun$apply$mcZ$sp$7$$anonfun$apply$mcZ$sp$8.apply$mcZ$sp(DatabricksFileSystemV2.scala:638)
```

## <a name="cause"></a>原因

如果之前在工作区装载了 ADLS Gen1 帐户但并未卸载，且用于该装载的凭据随后过期，则可能出现该错误。 尝试使用新凭据装载同一帐户时，已过期的凭据和新凭据之间会产生冲突。

## <a name="solution"></a>解决方案

需要卸载所有现有装载并使用新的、未过期的凭据创建新的装载。

有关详细信息，请参阅[使用 DBFS 装载 Azure Data Lake Storage Gen1](/databricks/data/data-sources/azure/azure-datalake.html#mount-azure-data-lake)。