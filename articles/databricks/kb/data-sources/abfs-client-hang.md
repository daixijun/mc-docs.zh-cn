---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
ms.date: 04/14/2020
title: ABFS 客户端在使用了错误的客户端 ID 或错误的路径时会挂起 - Azure Databricks
description: 使用 Azure Data Lake Storage Gen2 (ADLS) 时，尝试访问 Azure Blob 文件系统 (ABFS) 路径会导致命令挂起。
category: Connectivity
author: Shasidhar
db-author: shasidhar.eranti@databricks.com
ms.openlocfilehash: 167fdbc86ebb22a36d3c8d5dc37cf45cadc4f387
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589717"
---
# <a name="abfs-client-hangs-if-incorrect-client-id-or-wrong-path-used"></a>如果使用了错误的客户端 ID 或错误的路径，则 ABFS 客户端会挂起

## <a name="problem"></a>问题

你正在使用 Azure Data Lake Storage (ADLS) Gen2。 当你尝试从 Azure Databricks 群集访问 Azure Blob 文件系统 (ABFS) 路径时，命令会挂起。

启用调试日志，你可以在驱动程序日志中看到以下堆栈跟踪：

```console
Caused by: java.io.IOException: Server returned HTTP response code: 400 for URL: https://login.microsoftonline.com/b9b831a9-6c10-40bf-86f3-489ed83c81e8/oauth2/token
  at sun.net.www.protocol.http.HttpURLConnection.getInputStream0(HttpURLConnection.java:1894)
  at sun.net.www.protocol.http.HttpURLConnection.access$200(HttpURLConnection.java:91)
  at sun.net.www.protocol.http.HttpURLConnection$9.run(HttpURLConnection.java:1484)
  at sun.net.www.protocol.http.HttpURLConnection$9.run(HttpURLConnection.java:1482)
  at java.security.AccessController.doPrivileged(Native Method)
  at java.security.AccessController.doPrivilegedWithCombiner(AccessController.java:782)
  at sun.net.www.protocol.http.HttpURLConnection.getInputStream(HttpURLConnection.java:1481)
  at java.net.HttpURLConnection.getResponseCode(HttpURLConnection.java:480)
  at sun.net.www.protocol.https.HttpsURLConnectionImpl.getResponseCode(HttpsURLConnectionImpl.java:347)
  at shaded.databricks.v20180920_b33d810.org.apache.hadoop.fs.azurebfs.oauth2.AzureADAuthenticator.getTokenSingleCall(AzureADAuthenticator.java:254)
  ... 31 more
```

## <a name="cause"></a>原因

如果在群集上为 ABFS 的属性 `fs.azure.account.oauth2.client.id` 配置了错误的值，或者你尝试访问格式为 `abfss://myContainer@myStorageAccount.dfs.core.windows.net/...` 且 `myStorageAccount` 不存在的显式路径，则 ABFS 驱动程序最终会进入重试循环，变得无响应。 该命令最终会失败，但由于重试次数过多，因此看起来像是命令挂起。

如果尝试使用现有的存储帐户访问错误路径，则会看到 404 错误消息。 在这种情况下，系统不会挂起。

## <a name="solution"></a>解决方案

访问 ABFS 数据时，必须验证所有凭据是否准确无误。 还必须验证尝试访问的 ABFS 路径是否存在。 如果其中任何一项不正确，就会出现此问题。