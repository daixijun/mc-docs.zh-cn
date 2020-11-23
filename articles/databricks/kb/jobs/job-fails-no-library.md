---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 由于未安装库，Azure Databricks 作业失败 - Azure Databricks
description: 了解如何防止 Azure Databricks 作业因未安装库而失败。
ms.openlocfilehash: 3ec06281acfb50201d83e84fa5587f649cbd4780
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589868"
---
# <a name="azure-databricks-job-fails-because-library-is-not-installed"></a>由于未安装库，Azure Databricks 作业失败

## <a name="problem"></a>问题

Azure Databricks 作业失败的原因如下：作业需要的库尚未安装，导致了 `Import` 错误。

## <a name="cause"></a>原因

发生错误的原因如下：作业在所需的库尚未安装时即开始运行。 如果在以下任一情况下于群集上运行作业，则群集可能会在安装库时遇到延迟：

* 在库处于终止状态时启动现有群集。
* 启动使用共享库（安装在所有群集上的库）的新群集。

## <a name="solution"></a>解决方案

如果作业需要某些库，请确保将这些库作为依赖库附加到作业本身。 请参阅以下文章和步骤，了解如何在创建作业时设置依赖库。

[创建作业时将库添加为依赖库](/databricks/jobs#create-a-job)。

1. 打开“添加依赖库”对话框：

   > [!div class="mx-imgBorder"]
   > ![no-alternative-text](../_static/images/jobs/add-lib-azure.png)

2. 选择库：

   > [!div class="mx-imgBorder"]
   > ![no-alternative-text](../_static/images/jobs/choose-lib-azure.png)

3. 验证库：

   > [!div class="mx-imgBorder"]
   > ![no-alternative-text](../_static/images/jobs/dep-lib-azure.png)