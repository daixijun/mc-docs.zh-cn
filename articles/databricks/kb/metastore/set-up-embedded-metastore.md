---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 如何设置嵌入式 Apache Hive 元存储 - Azure Databricks
description: 了解如何使用 Azure Databricks 设置嵌入式 Apache Hive 元存储。
ms.openlocfilehash: 21a3dbf367473c4b553eb85699165ec7c10b1dfc
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589853"
---
# <a name="how-to-set-up-an-embedded-apache-hive-metastore"></a>如何设置嵌入式 Apache Hive 元存储

可以将 Azure Databricks 群集设置为使用嵌入式元存储。 只需在群集生命周期内保留表元数据时，可以使用嵌入式元存储。 如果群集重启，元数据将丢失。

如果群集重启后需保存表元数据或其他数据，则应使用默认元存储或设置外部元存储。

本示例使用 Apache Derby 嵌入式元存储，它是一种内存中轻量级数据库。 遵照笔记本中的说明安装元存储。

应始终先对测试群集执行此过程，然后才将该过程应用于其他群集。

## <a name="set-up-an-embedded-hive-metastore-notebook"></a>设置嵌入式 Hive 元存储笔记本

[获取笔记本](../_static/notebooks/metastore/set-up-embedded-metastore.html)