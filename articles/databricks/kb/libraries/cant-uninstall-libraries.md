---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 无法从 UI 卸载库 - Azure Databricks
description: 了解在无法使用 Azure Databricks 用户界面卸载库时该怎么做。
ms.openlocfilehash: 9834d84d98d5864a6565f57c7e53e613d622a862
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589963"
---
# <a name="cannot-uninstall-library-from-ui"></a>无法从 UI 卸载库

## <a name="problem"></a>问题

通常，可以在群集 UI 中卸载库。 如果用于选择库的复选框处于禁用状态，则无法从 UI 中卸载库。

## <a name="cause"></a>原因

如果使用 REST API 版本 1.2 创建库，并且启用了自动附加功能，则该库将安装在所有群集上。 在这种情况下，用于选择要卸载的库的群集 UI 复选框会被禁用。

## <a name="solution"></a>解决方案

创建一个工作区库，使其指向无法卸载的库的 DBFS 位置。

示例：无法卸载此 DBFS 位置上提供的 JAR 库：

```
dbfs:/Filestore/jars/custom_elastic_spark.jar
```

1. 创建一个指向同一 DBFS 位置的新工作区库。
2. 在库 UI 中，选中可从各个群集卸载库的复选框。