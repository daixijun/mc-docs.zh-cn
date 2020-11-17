---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 11/22/2019
title: 基因组学 - Azure Databricks
description: 了解如何使用用于基因组学的 Databricks 统一分析平台来处理基因组数据。
ms.openlocfilehash: a93611a73bfe48dcce2244d769338958f4f70e88
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589716"
---
# <a name="genomics"></a>基因组学

用于基因组学的 Databricks 统一分析平台是对大规模基因组学数据进行统一分析的平台，它增强了 [Databricks 统一数据分析平台](https://databricks.com/product/unified-data-analytics-platform)的所有功能，并利用了用于基因组学的 Databricks Runtime 提供的功能。

[用于基因组学的 Databricks Runtime](../../runtime/genomicsruntime.md#dbr-genomics) 提供两种类别的功能：

* Databricks-Regeneron 开放源代码项目 [Glow](https://projectglow.io) 提供的所有功能的优化版本。 Azure Databricks 是使用 Glow 的最佳平台，因为它提供了多种优化和无缝用户体验。 要了解 Glow 中的功能，请参阅 [Glow 文档](https://glow.readthedocs.io/en/latest/)。 你可以将 Glow 文档中的所有笔记本示例导入 Azure Databricks 工作区，并在使用用于基因组学的 Databricks Runtime 的群集上运行它们。
* 用于基因组学的 Databricks Runtime 特有的许多其他二级和三级分析功能。 有关这些功能的文档以及相关用例和 Azure Databricks 笔记本示例，请参阅：

  * [二级分析](secondary/index.md)
    * [DNASeq 管道](secondary/dnaseq-pipeline.md)
    * [RNASeq 管道](secondary/rnaseq-pipeline.md)
    * [“肿瘤/正常”管道](secondary/tumor-normal-pipeline.md)
    * [变异批注方法](secondary/annotate-variants.md)
  * [三级分析](tertiary/index.md)
    * [联合基因分型管道](tertiary/joint-genotyping-pipeline.md)
    * [GloWGR：整个基因组回归](tertiary/glowgr.md)
    * [SAIGE 和 SAIGE-GENE（使用管道转换器）](tertiary/saige.md)
    * [Hail 0.2](tertiary/hail.md)