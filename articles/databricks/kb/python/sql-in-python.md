---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
ms.date: 03/23/2020
title: 如何从 Python 脚本运行 SQL 查询 - Azure Databricks
description: 了解如何使用 Python 脚本运行 SQL 查询。
category: Spark Streaming
author: kr-arjun
db-author: arjun.kaimaparambilrajan@databricks.com
ms.openlocfilehash: c9bc8a878d78d28db84220d0df0b18db4ce34479
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589527"
---
# <a name="how-to-run-sql-queries-from-python-scripts"></a>如何从 Python 脚本运行 SQL 查询

你可能想要在 Azure Databricks 笔记本之外访问表。 除了[通过 JDBC 连接 BI 工具](/databricks/integrations/bi/jdbc-odbc-bi)外，还可以使用 Python 脚本访问表。 可以使用 [PyHive](https://github.com/dropbox/PyHive) 通过 JDBC 连接到 Spark 群集，然后运行脚本。 应在运行 Python 脚本的计算机上安装 PyHive。

> [!NOTE]
>
> Python 2 已被视为[生命周期结束](python-2-eol.md)。 应使用 Python 3 运行本文中提供的脚本。 如果你的系统上运行了 Python 2 和 Python 3，则在继续操作之前，应确保已将你的 pip 版本链接到 Python 3。
>
> 可以通过在命令提示符下运行 `pip -V` 来检查你的 pip 版本。 此命令返回 pip 版本以及它正在使用的 Python 版本。

## <a name="install-pyhive-and-thrift"></a>安装 PyHive 和 Thrift

请使用 pip 安装 PyHive 和 Thrift。

```bash
   pip install pyhive thrift
```

## <a name="run-sql-script"></a>运行 SQL 脚本

此示例 Python 脚本将 SQL 查询 `show tables` 发送到群集，然后显示查询结果。

运行脚本之前，请执行以下操作：

1. 将 `<token>` 替换为你的 Azure Databricks API 令牌。
2. 将 `<databricks-instance>` 替换为你的 Databricks 部署的域名。
3. 将 `<workspace-id>` 替换为工作区 ID。
4. 将 `<cluster-id>` 替换为群集 ID。

若要获取 API 令牌，请参阅[生成令牌](/databricks/dev-tools/api/latest/authentication#token-management)。 若要确定其他值，请参阅 [如何获取工作区、群集、笔记本和作业详细信息](/databricks/workspace/workspace-details)。

```python
#!/usr/bin/python

import os
import sys
from pyhive import hive
from thrift.transport import THttpClient
import base64

TOKEN = "<token>"
WORKSPACE_URL = "<databricks-instance>"
WORKSPACE_ID = "<workspace-id>"
CLUSTER_ID = "<cluster-id>"

conn = 'https://%s/sql/protocolv1/o/%s/%s' % (WORKSPACE_URL, WORKSPACE_ID, CLUSTER_ID)
print(conn)

transport = THttpClient.THttpClient(conn)

auth = "token:%s" % TOKEN
PY_MAJOR = sys.version_info[0]

if PY_MAJOR < 3:
  auth = base64.standard_b64encode(auth)
else:
  auth = base64.standard_b64encode(auth.encode()).decode()

transport.setCustomHeaders({"Authorization": "Basic %s" % auth})

cursor = hive.connect(thrift_transport=transport).cursor()

cursor.execute('show tables')
for table in cursor.fetchall():
    print(table)
```