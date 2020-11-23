---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 笔记本自动保存因文件大小限制而失败 - Azure Databricks
description: 了解 Azure Databricks 笔记本因文件大小限制而失败时要执行的操作。
ms.openlocfilehash: 2f5af38e77cf1c63eae26be59d27833cb0a8465e
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589449"
---
# <a name="notebook-autosave-fails-due-to-file-size-limits"></a>笔记本自动保存因文件大小限制而失败

## <a name="problem"></a>问题

笔记本自动保存失败后会显示以下错误消息：

```console
Failed to save revision: Notebook size exceeds limit. This is most commonly caused by cells with large results. Remove some cells or split the notebook.
```

## <a name="cause"></a>原因

允许自动保存的最大笔记本大小为 8 MB。

## <a name="solution"></a>解决方案

首先，使用浏览器的开发人员工具检查笔记本文件的大小。

例如，在 Chrome 中，单击“查看”>“开发人员”>“开发人员工具”。 单击“网络”选项卡并查看笔记本文件的“大小”列 。

> [!div class="mx-imgBorder"]
> ![笔记本大小](../_static/images/notebooks/browser-notebooksize.png)

下面是两种可能的解决方案：

1. 可以手动保存最多 32 MB 的笔记本。
2. 可以通过隐藏较大的结果来减小笔记本大小。

   绘图工具（例如 plotly 和 matplotlib）可以生成以大图像显示的大型结果集。 可以通过隐藏这些大型结果和图像来减小笔记本大小。