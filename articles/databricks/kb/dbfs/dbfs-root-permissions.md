---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/10/2020
title: 不能读取存储在 DBFS 根目录中的 Azure Databricks 对象 - Azure Databricks
description: 了解不能读取存储在 DBFS 根目录中的 Azure Databricks 对象时要执行的操作。
ms.openlocfilehash: 661cb3360389d5f62b5cdcaddcf69fc73ae4fb08
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589558"
---
# <a name="cannot-read-azure-databricks-objects-stored-in-the-dbfs-root-directory"></a>不能读取存储在 DBFS 根目录中的 Azure Databricks 对象

## <a name="problem"></a>问题

当你尝试从 Azure Databricks 群集外读取存储在 blob 存储中 DBFS 根目录中的 Azure Databricks 对象时，将返回 `Access Denied` 错误。

## <a name="cause"></a>原因

这是 DBFS 根目录的正常行为。 Azure Databricks 将库和其他临时系统文件等对象存储在 DBFS 根目录中。 Azure Databricks 是唯一可以读取这些对象的用户。

## <a name="solution"></a>解决方案

Azure Databricks 不建议使用根目录存储任何用户文件或对象。 请创建一个不同的 blob 存储目录，然后装载到 DBFS。