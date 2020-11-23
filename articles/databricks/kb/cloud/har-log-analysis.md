---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/10/2020
title: 如何分析用户界面性能问题 - Azure Databricks
description: 了解如何对 Azure Databricks 用户界面性能问题进行故障排除。
ms.openlocfilehash: ca79165915f6068a4738cac4895b8133db225523
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589807"
---
# <a name="how-to-analyze-user-interface-performance-issues"></a>如何分析用户界面性能问题

## <a name="problem"></a>问题

Azure Databricks 用户界面似乎运行缓慢。

## <a name="cause"></a>原因

用户界面性能问题通常是由于网络延迟或数据库查询所用的时间超过预期时间而导致的。

若要解决此类问题，需要收集网络日志并对其进行分析，了解哪些网络流量受到影响。

在大多数情况下，你将需要 Databricks 支持部门的协助来识别和解决 Databricks 用户界面性能问题，但也可以使用 [G Suite 工具箱 HAR 分析器](https://toolbox.googleapps.com/apps/har_analyzer/)等工具自行分析日志。 此工具可帮助分析日志并确定确切的 API 和每个请求所用的时间。

## <a name="troubleshooting-procedure"></a>故障排除过程

这是适用于 Google Chrome 浏览器的过程。 对于其他浏览器，请参阅 [G Suite 工具箱 HAR 分析器](https://toolbox.googleapps.com/apps/har_analyzer/)。

1. 打开 Google Chrome 浏览器，然后转到出现问题的页面。
2. 在 Chrome 菜单栏中，选择“查看”>“开发人员”>“开发人员工具”。
3. 在屏幕底部的面板中，选择“网络”选项卡。
4. 找到“网络”选项卡左上角上的圆形“记录”按钮，并确保其呈现红色 。 如果该按钮呈现灰色，请单击它以开始录制。
5. 勾选“保留日志”旁边的框。
6. 单击“清除”，清除“网络”选项卡中的所有现有日志 。
7. 在记录网络请求时重现问题。
8. 重现并记录问题之后，右键单击网络请求网格上的任意位置以打开上下文菜单，选择“使用“上下文”全部另存为带有内容的 HAR”，然后将文件保存到计算机。
9. 使用 HAR 分析器工具分析文件。 如果此分析不能解决问题，请打开支持工单并上传 HAR 文件或将其附加到电子邮件中，以便 Databricks 可以对其进行分析。

### <a name="example-output-from-har-analyzer"></a>HAR 分析器的示例输出

> [!div class="mx-imgBorder"]
> ![no-alternative-text](../_static/images/cloud/har-analyzer-screen.png)