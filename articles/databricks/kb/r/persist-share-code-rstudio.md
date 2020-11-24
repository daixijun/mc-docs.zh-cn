---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 如何在 RStudio 中持久保存和共享代码 - Azure Databricks
description: 了解如何在 Azure Databricks 与 RStudio 之间共享笔记本。
ms.openlocfilehash: 107fd6f1cee6b29f0ef662982b2a5f4d86a64a25
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589518"
---
# <a name="how-to-persist-and-share-code-in-rstudio"></a>如何在 RStudio 中持久保存和共享代码

## <a name="problem"></a>问题

与内置了版本控制的 Azure Databricks 笔记本不同，当关闭托管 Rstudio 的高并发群集时，在 RStudio 中开发的代码会丢失。

## <a name="solution"></a>解决方案

若要在 RStudio 中持久保存和共享代码，请执行以下操作之一：

* 在 RStudio 中，将代码保存到 DBFS 上的一个文件夹中，可从 Azure Databricks 笔记本和 RStudio 访问该文件夹。
* 在 RStudio 中使用 Git 之类的版本控制的集成支持。
* 将 R 笔记本作为 `Rmarkdown` 导出以将其保存到你的本地文件系统，然后将该文件导入到 RStudio 实例中。

[Sharing R Notebooks using RMarkdown](https://databricks.com/blog/2018/07/06/sharing-r-notebooks-using-rmarkdown.html)（使用 RMarkdown 共享 R 笔记本）这一博客文章更详细地介绍了这些步骤。

可以通过此过程持久保存在 RStudio 中开发的代码，并在 Azure Databricks 笔记本环境与 RStudio 之间共享笔记本。