---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/10/2020
title: 装载或访问 Azure Blob 存储时发生故障 - Azure Databricks
description: 了解如何解决从 Azure Databricks 装载或访问 Azure Blob 存储时发生故障的问题。
ms.openlocfilehash: 7bda6891b56d2c93ffb16b6c5f235bd5554d3756
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589625"
---
# <a name="failure-when-mounting-or-accessing-azure-blob-storage"></a>装载或访问 Azure Blob 存储时发生故障

## <a name="problem"></a>问题

尝试访问已创建的装入点或创建新的装入点时，该操作将失败并显示以下错误：

```console
WASB: Fails with java.lang.NullPointerException
```

## <a name="cause"></a>原因

当根装载路径（例如 `/mnt/`）也装载到 Blob 存储时，会发生此错误。 运行以下命令来检查是否还装载了根路径：

```python
dbutils.fs.mounts()
```

检查列表中是否出现 `/mnt`。

## <a name="solution"></a>解决方案

使用以下命令卸载 `/mnt/` 装入点：

```python
dbutils.fs.unmount("/mnt")
```

现在应该能够访问现有的装入点并创建新的装入点。