---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/10/2020
title: 无法在 JSON 中检测到编码 - Azure Databricks
description: 了解如何解决将 BOM 与 Azure Databricks 一起使用时无法检测到输入 JSON 文件的编码的问题。
ms.openlocfilehash: 8c305f96f401ab69ca97319959dde8a1e0ffcad8
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589759"
---
# <a name="failure-to-detect-encoding-in-json"></a>无法在 JSON 文件中检测到编码

## <a name="problem"></a>问题

Spark 作业失败，并出现包含以下消息的异常：

```console
Invalid UTF-32 character 0x1414141(above 10ffff)  at char #1, byte #7)
At org.apache.spark.sql.catalyst.json.JacksonParser.parse
```

## <a name="cause"></a>原因

JSON 数据源读取器能够使用文件开头的 [BOM](https://en.wikipedia.org/wiki/Byte_order_mark) 自动检测输入 JSON 文件的编码。
然而，BOM 不是 Unicode 标准所必需的，[RFC 7159](https://tools.ietf.org/html/rfc7159) 就禁止使用它，例如，8.1 节：

“…实现不得在 JSON 文本的开头添加字节顺序标记。”

因此，在某些情况下，Spark 无法正确检测字符集并读取 JSON 文件。

## <a name="solution"></a>解决方案

要解决此问题，请禁用字符集自动检测机制，并使用 `encoding` 选项显式设置字符集：

```scala
.option("encoding", "UTF-16LE")
```