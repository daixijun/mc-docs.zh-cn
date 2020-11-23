---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 使用 Python API 读取已装载 DBFS 的大型文件 - Azure Databricks
description: 了解如何解决使用 Python API 读取已装载 DBFS 的大型文件时出现的错误。
ms.openlocfilehash: 82f8bb61b8ecd18f30c1f404be80fba92bf2b495
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589844"
---
# <a name="reading-large-dbfs-mounted-files-using-python-apis"></a>使用 Python API 读取已装载 DBFS 的大型文件

本文介绍如何解决使用本地 Python API 读取已装载 DBFS 的大型文件时出现的错误。

## <a name="problem"></a>问题

如果将一个文件装载到 `dbfs://` 上并使用 pandas 之类的 Python API 读取大于 2GB 的文件，将显示以下错误：

```console
/databricks/python/local/lib/python2.7/site-packages/pandas/parser.so in pandas.parser.TextReader.__cinit__ (pandas/parser.c:3427)()
/databricks/python/local/lib/python2.7/site-packages/pandas/parser.so in pandas.parser.TextReader._setup_parser_source (pandas/parser.c:6883)()
IOError: Initializing from file failed
```

## <a name="cause"></a>原因

发生此错误是因为 Python 方法中用于读取文件的一个参数是有符号整数，文件的长度也是整数，并且如果该对象是大于 2GB 的文件，则该长度可以大于最大的有符号整数。

## <a name="solution"></a>解决方案

将文件从 `dbfs://` 移动到本地文件系统 `(file://)`。 然后使用 Python API 读取。 例如：

1. 将文件从 `dbfs://` 复制到 `file://`：

   ```bash
   %fs cp dbfs:/mnt/large_file.csv file:/tmp/large_file.csv
   ```

2. 读取 pandas API 中的文件：

   ```python
   import pandas as pd
   pd.read_csv('file:/tmp/large_file.csv',).head()
   ```