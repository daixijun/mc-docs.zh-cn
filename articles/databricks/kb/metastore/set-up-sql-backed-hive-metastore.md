---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 如何使用 Hive 2.0-2.2 在 SQL Server 上设置 Hive 元存储 - Azure Databricks
description: 了解如何在 SQL Server 上设置 Hive 元存储。
ms.openlocfilehash: 6662a328fbc7459780f093ff76b04529bf9c9537
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589852"
---
# <a name="how-to-set-up-hive-metastore-on-sql-server-with-hive-20-22"></a>如何使用 Hive 2.0-2.2 在 SQL Server 上设置 Hive 元存储

## <a name="problem"></a>问题

在 SQL Server 上设置 Hive 外部元存储时，由于 [HIVE-14698](https://issues.apache.org/jira/browse/HIVE-14698)，该操作会失败。

## <a name="version"></a>Version

Hive 2.0 至 2.2。

## <a name="solution"></a>解决方案

若要解决此问题，请导入笔记本，然后按照说明进行操作。 此笔记本中的说明针对的是 Hive 2.1。 不过，也可在不更改 Hive 2.0 和 2.2 的情况下使用这些说明。

### <a name="external-metastore-setup-hive-21-with-sql-server-notebook"></a>外部元存储使用 SQL Server 笔记本设置 Hive 2.1

[获取笔记本](../_static/notebooks/metastore/external-metastore-setup-hive-2.1-sqlserver.html)