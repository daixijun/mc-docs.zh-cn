---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
ms.date: 03/23/2020
title: 通过 Python 使用 HDFS API 读取文件 - Azure Databricks
description: 了解如何以 Python 使用 HDFS API 直接读取文件。
category: Storage
author: kr-arjun
db-author: arjun.kaimaparambilrajan@databricks.com
ms.openlocfilehash: 80b98b14a5585fd28fad2fa2295bd173be762a4a
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589835"
---
# <a name="use-the-hdfs-api-to-read-files-in-python"></a>通过 Python 使用 HDFS API 读取文件

有时你可能想直接读取文件而不使用第三方库。 当你的常规存储 Blob 无法用作本地 DBFS 装载时，这对于读取小文件很有用。

将以下示例代码用于 Azure Blob 存储。

```python
URI = sc._gateway.jvm.java.net.URI
Path = sc._gateway.jvm.org.apache.hadoop.fs.Path
FileSystem = sc._gateway.jvm.org.apache.hadoop.fs.FileSystem
conf = sc._jsc.hadoopConfiguration()

conf.set(
  "fs.azure.account.key.<account-name>.blob.core.windows.net,
  "<account-access-key>")

fs = Path('wasbs://<container-name>@<account-name>.blob.core.windows.net/<file-path>/').getFileSystem(sc._jsc.hadoopConfiguration())
istream = fs.open(Path('wasbs://<container-name>@<account-name>.blob.core.windows.net/<file-path>/'))

reader = sc._gateway.jvm.java.io.BufferedReader(sc._jvm.java.io.InputStreamReader(istream))

while True:
  thisLine = reader.readLine()
  if thisLine is not None:
    print(thisLine)
  else:
    break

istream.close()
```

where

* `<account-name>` 是你的 Azure 帐户名称。
* `<container-name>` 是容器名称。
* `<file-path>` 是文件的完整路径。
* `<account-access-key>` 是帐户访问密钥。