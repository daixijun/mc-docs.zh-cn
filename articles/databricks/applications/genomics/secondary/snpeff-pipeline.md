---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/04/2020
title: 预先打包的 SnpEff 批注管道 - Azure Databricks
description: 了解如何使用用于基因组学的 Databricks Runtime 中包含的 SnpEff 管道。
ms.openlocfilehash: b2f628be2279d52a3836bd050e1dd29b711027f2
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589700"
---
# <a name="pre-packaged-snpeff-annotation-pipeline"></a><a id="pre-packaged-snpeff-annotation-pipeline"> </a><a id="snpeff-pipeline"> </a>预打包的 SnpEff 批注管道

## <a name="setup"></a>设置

将 [SnpEff](http://snpeff.sourceforge.net/) (v4.3) 作为 Azure Databricks 作业运行。 最有可能的是，Azure Databricks 解决方案架构师将为你设置初始作业。 必要的详细信息如下：

* 群集配置
  * [用于基因组学的 Databricks Runtime](../../../runtime/genomicsruntime.md#dbr-genomics)
* 将任务设置为已导入工作区中的 [SnpEffAnnotationPipeline 笔记本](#snpeff-notebook)。

## <a name="benchmarks"></a>基准

该管道已在 1000 基因组项目的 8520 万个变体站点上使用以下群集配置进行了测试：

* 驱动程序：Standard_DS13_v2
* 辅助角色：Standard_D32s_v3 * 7（224 个核心）
* 运行时：2.5 小时

## <a name="parameters"></a>参数

管道接受多个控制其行为的参数。 这里记录了最重要且最常更改的参数；其余参数可以在 SnpEff 批注管道笔记本中找到。 可以为所有运行或单次运行设置所有参数。

| 参数                   | 默认     | 描述                                                         |
|-----------------------------|-------------|---------------------------------------------------------------------|
| inputVariants               | 不适用         | 输入变量（VCF 或 Delta Lake）的路径。                         |
| 输出                      | 不适用         | 应将管道输出写入到的路径。                   |
| exportVCF                   | false       | 如果为 true，则管道会将结果写入 VCF 以及 Delta Lake。  |
| exportVCFAsSingleFile       | false       | 如果为 true，则将 VCF 作为单一文件导出                                 |

此外，必须使用[环境变量](../../../clusters/configure.md#environment-variables)来配置参考基因组。
若要使用 Grch37，请设置环境变量：

```ini
refGenomeId=grch37
```

若要改用 Grch38，请按如下所示设置环境变量：

```ini
refGenomeId=grch38
```

## <a name="output"></a>输出

带批注的变体将写出到提供的输出目录内的 Delta 表中。
如果将管道配置为导出到 VCF，这些变体也会显示在输出目录下。

```
output
|---annotations
    |---Delta files
|---annotations.vcf
```

### <a name="snpeff-annotation-pipeline-notebook"></a><a id="snpeff-annotation-pipeline-notebook"> </a><a id="snpeff-notebook"> </a>SnpEff 批注管道笔记本

[获取笔记本](../../../_static/notebooks/genomics/snpeff-annotation-pipeline.html)