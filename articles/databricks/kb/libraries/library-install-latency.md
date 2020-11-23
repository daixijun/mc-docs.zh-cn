---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 库不可用导致作业失败 - Azure Databricks
description: 了解如何解决因不可用的库而导致的 Azure Databricks 作业失败。
ms.openlocfilehash: c23fdfdf371fbdf4eac7d5d69fd7560d284d8085
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589906"
---
# <a name="library-unavailability-causing-job-failures"></a>库不可用导致作业失败

本文介绍了在启动导入外部库的作业时可能会遇到的导入错误。

## <a name="problem"></a>问题

当作业导致节点重启时，作业会失败，并出现以下错误消息：

```bash
ImportError: No module named XXX
```

## <a name="cause"></a>原因

群集管理器是用于管理客户 Apache Spark 群集的 Azure Databricks 服务的一部分。 它在重启每个节点时，会发送命令来安装 Python 和 R 库。 有时，安装库或从 internet 下载项目所耗时间可能比预期长。 这是由于网络延迟导致的；如果附加到群集的库有许多依赖库，也会发生这种情况。

库安装机制保证当笔记本附加到群集时，可导入已安装的库。 如果通过 PyPI 安装库的时间过长，笔记本将在库安装完成之前附加到群集。 在这种情况下，笔记本无法导入库。

## <a name="solution"></a>解决方案

### <a name="method-1"></a>方法 1

在笔记本中使用笔记本范围内的库安装命令。 可在一个单元格中输入以下命令，确保已安装所有指定的库。

```bash
dbutils.library.installPyPI("mlflow")
dbutils.library.restartPython()
```

### <a name="method-2"></a>方法 2

若要避免从 Internet 存储库下载库时出现延迟，可在 DBFS 或 Azure Blob 存储中缓存库。

例如，可将 Python 库的 wheel 或 egg 文件下载到 DBFS 或 Azure Blob 存储位置。 可使用 REST API 或群集范围的 init 脚本从 DBFS 或 Azure Blob 存储安装库。

首先，将 wheel 或 egg 文件从 Internet 下载到 DBFS 或 Azure Blob 存储位置。 可在笔记本中执行此操作，如下所示：

```bash
%sh
cd /dbfs/mnt/library
 wget <whl/egg file location from the pypi repository>
```

下载 wheel 或 egg 文件后，可使用 REST API、UI 或 init 脚本命令将库安装到群集。