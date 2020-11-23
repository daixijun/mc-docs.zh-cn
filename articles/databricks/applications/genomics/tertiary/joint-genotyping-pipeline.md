---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 07/02/2020
title: 联合基因分型管道 - Azure Databricks
description: 了解如何使用用于基因组学的 Databricks Runtime 中包含的联合基因分型管道。
ms.openlocfilehash: 0cb0ec7096d6ed97e0d6330136d06613f8ea5590
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589685"
---
# <a name="joint-genotyping-pipeline"></a>联合基因分型管道

Azure Databricks 联合基因分型管道是 [GATK 最佳做法](https://gatk.broadinstitute.org/hc/en-us/articles/360035535932-Germline-short-variant-discovery-SNPs-Indels-)兼容管道，用于使用 [GenotypeGVCFs](https://gatk.broadinstitute.org/hc/en-us/articles/360041417092-GenotypeGVCFs) 进行联合基因分型。

## <a name="walkthrough"></a>演练

管道通常包括以下步骤：

1. 将突变引入到 Delta Lake。
2. 使用 GenotypeGVCF 联合调用队列。

在突变引入过程中，分批处理单样本 gVCF，并将行存储在 Delta Lake 中，以提供容错、快速查询和增量联合基因分型。 在联合基因分型步骤中，从 Delta Lake 提取 gVCF 行，将其拆分为箱，然后将其分发到分区。 对于每个突变位点，将识别每个样本的相关 gVCF 行并将其用于基因再分型。

## <a name="setup"></a>设置

管道将作为 Azure Databricks 作业运行。 可能会有 Azure Databricks 解决方案架构师很与你协作，以设置初始作业。 必要的详细信息如下：

```json
{
  "autoscale.min_workers": {
    "type": "unlimited",
    "defaultValue": 1
  },
  "autoscale.max_workers": {
    "type": "unlimited",
    "defaultValue": 25
  },
  "node_type_id": {
    "type": "unlimited",
    "defaultValue": "Standard_L32s_v2"
  },
  "spark_env_vars.refGenomeId": {
    "type": "unlimited",
    "defaultValue": "grch38"
  },
  "spark_version": {
    "type": "regex",
    "pattern": ".*-hls.*",
    "defaultValue": "7.0.x-hls-scala2.12"
  }
}
```

* 群集配置应使用[用于基因组学的 Databricks Runtime](../../../runtime/genomicsruntime.md#dbr-genomics)。
* 此任务应为此页面底部提供的联合基因分型管道笔记本。
* 若要获得最佳性能，请使用针对存储进行了优化的 VM。 建议使用 Standard_L32s_v2。
* 若要降低成本，请启用自动缩放，根据延迟要求最少保留 1 个辅助角色，最多扩展到 10-50 个。

## <a name="parameters"></a>参数

管道接受控制其行为的参数。 此处记录了最重要且最常更改的参数。 若要查看所有可用参数及其使用信息，请运行管道笔记本的第一个单元。 定期添加新的参数。
可以为所有运行或每次运行设置参数。

| 参数                         | 默认                           | 描述                                                                                                                                                                                             |
|-----------------------------------|-----------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| manifest                          | 不适用                               | 描述输入的[清单](#manifest-format)。                                                                                                                                                  |
| 输出                            | 不适用                               | 写入管道[输出](#output)的路径。                                                                                                                                                   |
| replayMode                        | skip                              | 下列其中一项：<br><br>* `skip`：如果输出已存在，则跳过相关阶段。<br>* `overwrite`：将删除现有输出。                                                                                     |
| exportVCF                         | false                             | 如果为 true，则管道会将结果写入 VCF 以及 Delta Lake。                                                                                                                                      |
| targetedRegions                   | 不适用                               | 包含要调用的区域的文件的路径。 如果省略，则调用所有区域。                                                                                                                                |
| genotypeGivenAlleles              | false                             | 如果为 true，则根据输入 gVCF 中的等位基因对突变位点进行基因再分型。                                                                                                                             |
| emitAllSites                      | false                             | 如果为 true，请在输出中保留低质量的位点。                                                                                                                                                        |
| gvcfDeltaOutput                   | 不适用                               | 如果指定，则在进行基因分型之前，会将 gVCF 引入到 Delta 表中。 仅应在希望多次联合调用同一 gVCF 时指定此参数。                                      |
| performValidation                 | false                             | 如果为 `true`，则系统会验证每个记录是否包含用于联合基因分型的必要信息。 特别是，它会检查是否存在正确数量的基因型概率。        |
| validationStringency              | STRICT                            | 在加载和验证过程中如何处理格式错误的记录。<br><br>* `STRICT`：作业失败<br>* `LENIENT`：记录警告并删除记录<br>* `SILENT`：删除记录且不发出警告 |

> [!TIP]
>
> * 若要保留极少数突变，请将 `genotypeGivenAlleles` 和 `emitAllSites` 设置为 `true`。 这等同于将 GATK 设置 `genotyping_mode` 从 `DISCOVERY`（选择最可能的等位基因）更改为 `GENOTYPE_GIVEN_ALLELES`（使用输入 gVCF 中存在的等位基因），并将 `output_mode` 从 `EMIT_VARIANTS_ONLY` （仅在突变位点生成调用）更改为 `EMIT_ALL_SITES`（无论置信度如何，都会在任何可调用的位点生成调用）。
> * 若要从现有 Delta 表执行联合调用，请将 `gvcfDeltaOutput` 设置为表路径，并将 `replayMode` 设置为 `skip`。 还可以提供 `manifest`，它将用于定义 VCF 架构和样本，否则，将从 Delta 表中推断出它们。 忽略此设置中的 `targetedRegions` 和 `performValidation` 参数。

## <a name="output"></a><a id="manifest-format"> </a><a id="output"> </a>输出

基因再分型的突变均写入到提供的输出目录内的 Delta 表中。 此外，如果将管道配置为导出 VCF，这些变体也会显示在输出目录下。

```
output
|---genotypes
    |---Delta files
|---genotypes.vcf
    |---VCF files
```

## <a name="reference-genomes"></a>参考基因组

必须使用[环境变量](../../../clusters/configure.md#environment-variables)来配置参考基因组。
若要使用 GRCh37，请设置环境变量：

```ini
refGenomeId=grch37
```

若要使用 GRCh38，请将 `grch37` 更改为 `grch38`。

若要使用自定义参考基因组，请参阅[自定义参考基因组](../secondary/dnaseq-pipeline.md#custom-reference-genomes)中的说明。

## <a name="manifest-format"></a>清单格式

> [!NOTE]
>
> 用于基因组学的 Databricks Runtime 6.6 及更高版本支持清单 blob。

清单是一个文件或 blob，用于描述查找输入 GVCF 文件的位置，其中每个路径都位于新行中。

> [!TIP]
>
> 如果提供的清单是文件，则每一行可以是绝对路径或相对于清单文件的路径。 如果提供的清单是 blob，则行字段必须是绝对路径。 可以包含 glob `(*)`，以匹配许多文件。

## <a name="troubleshooting"></a>疑难解答

**作业由于出现 `ArrayIndexOutOfBoundsException` 而失败**

此错误通常表明输入记录的基因型概率数量不正确。 尝试将 `performValidation` 选项设置为 `true`，并将 `validationStringency` 选项设置为 `LENIENT` 或 `SILENT`。

## <a name="additional-usage-info"></a>其他使用信息

联合基因分型管道与其他 Azure Databricks 管道有着许多相同的操作详细信息。 有关更详细的用法信息，例如输出格式结构、以编程方式运行的提示以及设置自定义参考基因组的步骤，请参阅 [DNASeq 管道](../secondary/dnaseq-pipeline.md)。

### <a name="joint-genotyping-pipeline-notebook"></a>联合基因分型管道笔记本

[获取笔记本](../../../_static/notebooks/genomics/joint-genotyping-pipeline.html)