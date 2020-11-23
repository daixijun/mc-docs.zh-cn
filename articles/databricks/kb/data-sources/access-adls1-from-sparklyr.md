---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/10/2020
title: 使用 Sparklyr 从 ADLS Gen1 读取数据时出错 - Azure Databricks
description: 了解如何解决使用 Azure Databricks 中的 Sparklyr 从 Azure Data Lake Storage Gen1 读取数据时出现的错误。
ms.openlocfilehash: 8d5cfca92234aa938e899f53d0f1bd4850458c0f
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589773"
---
# <a name="error-when-reading-data-from-adls-gen1-with-sparklyr"></a>使用 Sparklyr 从 ADLS Gen1 读取数据时出错

## <a name="problem"></a>问题

使用已启用 Azure AD 凭据传递的群集时，在该群集上运行的命令可在 Azure Data Lake Storage Gen1 中读取和写入数据，而无需你配置用于访问存储的服务主体凭据。

例如，你可以使用以下命令直接访问数据

```python
spark.read.csv("adl://myadlsfolder.azuredatalakestore.net/MyData.csv").collect()
```

然而，当你尝试使用 Sparklyr 直接访问数据时：

```r
spark_read_csv(sc, name = "air", path = "adl://myadlsfolder.azuredatalakestore.net/MyData.csv")
```

该操作将失败并显示以下错误：

```console
com.databricks.backend.daemon.data.client.adl.AzureCredentialNotFoundException: Could not find ADLS Gen1 Token
```

## <a name="cause"></a>原因

Sparklyr 中的 `spark_read_csv` 函数无法提取 ADLS 令牌以启用身份验证和读取数据。

## <a name="solution"></a>解决方案

一种解决方法是使用 Azure `application id`、`application key` 和 `directory id` 在 DBFS 中安装 ADLS 位置：

```python
# Get credentials and ADLS URI from Azure
applicationId= <application-id>
applicationKey= <application-key>
directoryId= <directory-id>
adlURI=<adl-uri>
assert adlURI.startswith("adl:"), "Verify the adlURI variable is set and starts with adl:"

# Mount ADLS location to DBFS
dbfsMountPoint=<mount-point-location>
dbutils.fs.mount(
  mount_point = dbfsMountPoint,
  source = adlURI,
  extra_configs = {
    "dfs.adls.oauth2.access.token.provider.type": "ClientCredential",
    "dfs.adls.oauth2.client.id": applicationId,
    "dfs.adls.oauth2.credential": applicationKey,
    "dfs.adls.oauth2.refresh.url": "https://login.microsoftonline.com/{}/oauth2/token".format(directoryId)
  })
```

然后，在 R 代码中使用装入点读取数据：

```r
# Install Sparklyr
%r
install.packages("sparklyr")
library(sparklyr)
# Create a sparklyr connection
sc <- spark_connect(method = "databricks")

# Read Data
%r
myData = spark_read_csv(sc, name = "air", path = "dbfs:/<mount-point-location>/myData.csv")
```