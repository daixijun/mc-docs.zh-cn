---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
ms.date: 06/08/2020
title: 正在阻止 ADLS 和 WASB 写入 - Azure Databricks
description: 了解如何解决“文件和文件夹的创建速率过高”的 ADLS 或 WASB 存储错误
category: Data Sources
author: adampavDB
db-author: adam.pavlacka@databricks.com
ms.openlocfilehash: 11c3f52e48c7836dff9c86aa87bb472b26a71678
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589769"
---
# <a name="adls-and-wasb-writes-are-being-throttled"></a>已阻止 ADLS 和 WASB 写入

## <a name="problem"></a>问题

访问 Azure data Lake Storage (ADLS) 上存储的数据时，Windows Azure 存储 Blob (WASB) 请求开始超时。你可能会看到一条错误消息，指出正在以太高的速率访问存储。

```console
Files and folders are being created at too high a rate
```

## <a name="cause"></a>原因

Azure 存储订阅对一段时间内可以访问的文件和文件夹数量有限制。 如果在给定时间范围内发出过多请求，则你的帐户会受到限制，防止请求数超出订阅限制。

## <a name="solution"></a>解决方案

若要解决此问题，可以提高 Azure 订阅的存储限制，也可以优化 Spark 代码以减少创建的文件数。