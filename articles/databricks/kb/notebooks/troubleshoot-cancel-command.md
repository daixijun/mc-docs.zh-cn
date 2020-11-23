---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 排查 Python 笔记本无响应或取消命令的问题 - Azure Databricks
description: 了解如何排查 Azure Databricks 笔记本中 Python 笔记本无响应或取消命令的问题。
ms.openlocfilehash: 3a77d86b2ce6a35870510d57de00f75ef63391fc
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589545"
---
# <a name="troubleshooting-unresponsive-python-notebooks-or-canceled-commands"></a>排查 Python 笔记本无响应或取消命令的问题

本文概述了当笔记本无响应或取消命令时可执行的故障排除步骤。

## <a name="check-metastore-connectivity"></a>检查元存储连接性

### <a name="problem"></a>问题

新附加的笔记本中的简单命令会失败，但之前已附加到相同群集的笔记本中的简单命令会成功。

### <a name="troubleshooting-steps"></a>疑难解答步骤

1. 检查元存储连接性。 无法连接到 Hive 元存储库可能会导致 REPL 初始化挂起，使群集显示为无响应。
2. 你使用的是 Azure Databricks 元存储还是你自己的外部元存储？ 如果你使用的是外部元存储，最近是否进行了任何更改？ 是否升级了元存储版本？ 是否轮替了密码或配置？ 是否更改了安全组规则？

有关更多故障排除提示和解决方法，请参阅[元存储：提示和故障排除](../metastore/index.md#metastore)。

## <a name="check-for-conflicting-libraries"></a>检查是否有冲突的库

### <a name="problem"></a>问题

Python 库冲突可能会导致命令被取消。 Azure Databricks 支持组织最常在 `ipython`、`numpy`、`scipy` 和 `pandas` 版本中看到冲突。

### <a name="troubleshooting-steps"></a>疑难解答步骤

请参阅[群集因库冲突而取消 Python 命令执行](../python/python-command-cancelled.md#python-command-cancelled)。

有关笔记本故障排除的详细信息，请参阅[笔记本：提示和故障排除](index.md#notebooks)。