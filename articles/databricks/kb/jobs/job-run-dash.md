---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
ms.date: 09/24/2020
title: 使用作业运行仪表板监视正在运行的作业 - Azure Databricks
description: 了解如何在工作区中使用作业运行仪表板。
category: Jobs
author: adampavDB
db-author: adam.pavlacka@databricks.com,nikhil.bharadwaj@databricks.com
ms.openlocfilehash: c78482b98474a9c369755cf6d62fcc7c292eb7c3
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589829"
---
# <a name="monitor-running-jobs-with-a-job-run-dashboard"></a>使用作业运行仪表板监视正在运行的作业

作业运行仪表板是一个笔记本，显示有关工作区中当前运行的所有作业的信息。

要配置仪表板，你必须具有将笔记本附加到要监视的工作区中的通用群集的权限。 如果不存在通用群集，则必须具有创建群集的权限。

配置仪表板后，你可以管理[作业权限](/databricks/security/access-control/jobs-acl#configure-job-permissions)，并将“可以查看”权限分配给组织中的用户。 这些用户可以查看仪表板，但不能修改它。

## <a name="job-run-dashboard-notebook"></a>运业运行仪表板笔记本

[获取笔记本](../_static/notebooks/jobs/job-run-dashboard.html)

## <a name="attach-the-dashboard"></a>附加仪表板

由于作业运行仪表板是笔记本，因此[将笔记本附加到群集](/databricks/notebooks/notebooks-manage#attach-a-notebook-to-a-cluster)不需要任何特殊步骤。

将其附加到通用群集。

## <a name="run-the-dashboard-as-a-scheduled-job"></a>将仪表板作为计划作业运行

将笔记本附加到工作区中的群集后，将其配置为每分钟运行一次的计划作业。

1. 打开笔记本。
2. 单击 ![计划](../_static/images/notebooks/schedule.png) 在笔记本工具栏中。
3. 在“计划作业”窗格中，单击“新建” 。
4. 在“创建计划”对话框中选择“每”和“分钟”  。
5. 单击“确定”。
6. 在“计划作业”窗格中单击“作业运行仪表板” 。
7. 在[作业详细信息](/databricks/jobs#job-details)页上，在“群集”选项旁边单击“编辑” 。
8. 选择现有通用群集。
9. 单击“确认”  。

## <a name="display-dashboard"></a>显示仪表板

1. 转到计划作业的作业详细信息页。
2. 检查以确保至少成功运行一次。
3. 单击“最近成功运行(自动刷新)”。
4. 选择“作业运行仪表板”视图。

仪表板现在处于演示模式。 在每次计划运行完成后，它会自动更新。

你可以与具有查看权限的任何用户共享仪表板 URL。

## <a name="results-listed"></a>列出的结果

作业运行仪表板结果分为两个部分：

* **作业运行** - 显示当前正在运行的所有计划作业。
* **运行提交** - 显示通过 API 调用调用的所有正在运行的作业。

仪表板显示每个作业的以下组件：

* **作业 ID** - 这是作业的唯一 ID 号。 可以通过将此 ID 输入[作业 URL](/databricks/workspace/workspace-details#job-url-and-id) 查看所有作业数据。
* **运行页** - 这是给定作业的特定运行的 ID 号。 它被格式化为可单击的超链接，因此你可以从作业运行仪表板直接导航到运行页。 通过导航到作业 URL，然后从已完成的运行列表中单击特定的运行页，可以访问以前的运行页。
* **运行名称** - 这是与作业相关的笔记本的名称。
* **开始时间** - 这是作业开始运行的时间。 时间显示格式为 `DD-MM-YYYY HH:MM:SS`，采用 24 小时制。 时间采用 UTC。
* **创建者** - 这是拥有此作业的用户的电子邮件地址。