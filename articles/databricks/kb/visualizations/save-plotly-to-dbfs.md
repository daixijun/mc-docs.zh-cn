---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 如何保存 Plotly 文件并从 DBFS 进行显示 - Azure Databricks
description: 了解如何保存 Plotly 文件并从 DBFS 显示这些文件。
ms.openlocfilehash: 9b9983086d317ba967111812508fb2c3d0d91dff
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589566"
---
# <a name="how-to-save-plotly-files-and-display-from-dbfs"></a>如何保存 Plotly 文件并从 DBFS 进行显示

可以将使用 Plotly 生成的图表作为 `jpg` 或 `png` 文件保存到驱动程序节点。 然后，可以使用 `displayHTML()` 方法在笔记本中显示它。
默认情况下，会将 Plotly 图表保存到群集中驱动程序节点上的 `/databricks/driver/` 目录。 以后使用以下过程显示该图表。

1. 生成示例绘图：

   ```python
   data = {'data': [{'y': [4, 2, 3, 4]}],
               'layout': {'title': 'Test Plot',
                          'font': dict(size=16)}}
   p = plot(data,output_type='div')
   displayHTML(p)
   ```

   > [!div class="mx-imgBorder"]
   > ![no-alternative-text](../_static/images/visualizations/sample-plot.png)

2. 使用 `plotly.io.write_image()` 将生成的绘图保存到文件中：

   ```bash
   plotly.io.write_image(fig=data,file="/databricks/driver/plotly_images/<imageName>.jpg", format="jpeg",scale=None, width=None, height=None)
   ```

3. 从驱动程序节点复制文件并将其保存到 DBFS：

   ```bash
   dbutils.fs.cp("file:/databricks/driver/plotly_images/<imageName>.jpg", "dbfs:/FileStore/<your_folder_name>/<imageName>.jpg")
   ```

4. 使用 `displayHTML()` 显示图像：

   ```bash
   displayHTML('''<img src="/files/<your_folder_name>/<imageName>.jpg">''')
   ```

另请参阅 [Python 和 R 笔记本中的 Plotly](/databricks/notebooks/visualizations/plotly)。