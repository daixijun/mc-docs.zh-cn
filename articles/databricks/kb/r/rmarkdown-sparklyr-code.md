---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 呈现包含 sparklyr 代码的 R Markdown 文件失败 - Azure Databricks
description: 了解如何解决在呈现包含 sparklyr 的 R Markdown 文件时出现的故障。
ms.openlocfilehash: aa0545b104ca85b0edcc0fb2ae09a9ed335cd0a2
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589512"
---
# <a name="rendering-an-r-markdown-file-containing-sparklyr-code-fails"></a>呈现包含 `sparklyr` 代码的 R Markdown 文件时失败

## <a name="problem"></a>问题

在 Azure Databricks 环境中安装并配置好 RStudio 之后，启动 RStudio 并单击“编织”按钮以编织一个包含 `sparklyr` 上下文初始化代码的 Markdown 文件时，呈现失败并出现以下错误：

```console
failed to start sparklyr backend:object 'DATABRICKS_GUID' not found Calls: <Anonymous>… tryCatch -> tryCatchList-> tryCatchOne -> <Anonymous> Execution halted
```

## <a name="cause"></a>原因

如果尝试使用类似以下内容的代码在 Markdown 笔记本中初始化 `sparklyr` 上下文，则 Markdown 页将无法呈现，因为 `knitr` 进程会生成缺少 `'DATABRICKS_GUID'` 全局变量的新命名空间。

```r
case library(sparklyr)
sc <- spark_connect(method = "databricks")
```

## <a name="solution"></a>解决方案

请使用以下脚本来呈现 Markdown 页，而不是单击 R Markdown 控制台中的“编织”按钮：

```r
rmarkdown::render("your_doc.Rmd")
```

使用 R 控制台呈现 Markdown 文件，然后就可以在 RStudio“文件”选项卡中访问该文件。

> [!div class="mx-imgBorder"]
> ![no-alternative-text](../_static/images/r/rstudio-files-tab.png)