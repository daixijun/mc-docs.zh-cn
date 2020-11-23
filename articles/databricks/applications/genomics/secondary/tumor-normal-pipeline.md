---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 09/29/2020
title: 肿瘤/正常管道 - Azure Databricks
description: 了解如何使用用于基因组学的 Databricks Runtime 中包含的肿瘤/正常管道。
ms.openlocfilehash: 1cb9f214d7dff9026388bba5985d50d6c7e84834
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589701"
---
# <a name="tumornormal-pipeline"></a>“肿瘤/正常”管道

Azure Databricks 肿瘤/正常管道是 [GATK 最佳做法](https://gatk.broadinstitute.org/hc/en-us/articles/360035894731-Somatic-short-variant-discovery-SNVs-Indels-)兼容管道，用于使用 [MuTect2](https://gatk.broadinstitute.org/hc/en-us/articles/360041416112-Mutect2) 基因突变鉴定软件进行短读比对和识别体细胞突变。

## <a name="walkthrough"></a>演练

管道包括以下步骤：

1. 使用 BWA-MEM 进行正常样本比对。
2. 使用 BWA-MEM 进行肿瘤样本比对。
3. 使用 MuTect2 识别突变。

## <a name="setup"></a>设置

管道将作为 Azure Databricks 作业运行。 可以设置[群集策略](../../../administration-guide/clusters/policies.md)以保存配置：

```json
{
  "num_workers": {
    "type": "unlimited",
    "defaultValue": 13
  },
  "node_type_id": {
    "type": "unlimited",
    "defaultValue": "Standard_F32s_v2"
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

* 群集配置应使用用于基因组学的 Databricks Runtime。
* 此任务应为此页面底部提供的肿瘤/正常笔记本。
* 若要获得最佳性能，请使用至少具有 60GB 内存的经过计算优化的 VM。 建议使用 Standard_F32s_v2 VM。
* 如果正在运行碱基质量分数重校准，请改用常规用途 (Standard_D32s_v3) 实例，因为此操作需要更多内存。

## <a name="parameters"></a>参数

管道接受控制其行为的参数。 此处记录了最重要且最常更改的参数。 若要查看所有可用参数及其使用信息，请运行管道笔记本的第一个单元。 定期添加新的参数。
可以为所有运行或每次运行设置参数。

| 参数                         | 默认                           | 描述                                                                                                                                                                                                                                                                      |
|-----------------------------------|-----------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| manifest                          | 不适用                               | 描述输入的清单。                                                                                                                                                                                                                                               |
| 输出                            | 不适用                               | 应写入管道输出的路径。                                                                                                                                                                                                                                |
| replayMode                        | skip                              | * 如果 `skip`输出已存在，则跳过相关阶段。<br>* 如果 `overwrite`，将删除现有输出。                                                                                                                                                              |
| exportVCF                         | false                             | 如果为 true，则管道会将结果写入 VCF 文件以及 Delta 中。                                                                                                                                                                                                             |
| perSampleTimeout                  | 12 小时                               | 每个样本应用的超时。 达到此超时时间后，管道将继续执行下一个样本。 此参数的值必须包含超时单位：“s”（表示秒）、“m”（表示分钟）或“h”（表示小时）。 例如，“60m”将使超时值为 60 分钟。 |

> [!TIP]
>
> 若要优化运行时，请在 Spark 配置中将 `spark.sql.shuffle.partitions` 配置为群集核心数的三倍。

## <a name="reference-genomes"></a>参考基因组

必须使用[环境变量](../../../clusters/configure.md#environment-variables)来配置参考基因组。
若要使用 GRCh37，请按如下所示设置环境变量：

```bash
refGenomeId=grch37
```

若要使用 GRCh38，请将 `grch37` 更改为 `grch38`。

若要使用自定义参考基因组，请参阅[自定义参考基因组](dnaseq-pipeline.md#custom-reference-genomes)中的说明。

## <a name="manifest-format"></a>清单格式

> [!NOTE]
>
> 用于基因组学的 Databricks Runtime 6.6 及更高版本支持清单 blob。

清单是一个 CSV 文件或 blob，用于描述在何处查找输入 FASTQ 或 BAM 文件。 示例：

```
pair_id,file_path,sample_id,label,paired_end,read_group_id
HG001,*_R1_*.normal.fastq.bgz,HG001_normal,normal,1,read_group_normal
HG001,*_R2_*.normal.fastq.bgz,HG001_normal,normal,2,read_group_normal
HG001,*_R1_*.tumor.fastq.bgz,HG001_tumor,1,tumor,read_group_tumor
HG001,*_R2_*.tumor.fastq.bgz,HG001_tumor,2,tumor,read_group_tumor
```

如果输入包含未比对的 BAM 文件，则应省略 `paired_end` 字段：

```
pair_id,file_path,sample_id,label,paired_end,read_group_id
HG001,*.normal.bam,HG001_normal,normal,,read_group_tumor
HG001,*.tumor.bam,HG001_tumor,tumor,,read_group_normal
```

给定个体的肿瘤样本和正常样本按 `pair_id` 字段分组。 一对肿瘤样本和正常样本名称读取组名称必须是不同的。

> [!TIP]
>
> 如果提供的清单是一个文件，则每行中的 `file_path` 字段可以是绝对路径或相对于清单文件的路径。 如果提供的清单是 blob，则 `file_path` 字段必须是绝对路径。 可以包含 glob `(*)`，以匹配许多文件。

## <a name="additional-usage-info-and-troubleshooting"></a>其他使用信息和故障排除

肿瘤/正常管道与其他 Azure Databricks 管道有着许多相同的操作详细信息。 有关更详细的用法信息（例如输出格式结构、以编程方式运行的提示以及设置自定义参考基因组的步骤）和常见问题，请参阅 [DNASeq 管道](dnaseq-pipeline.md)。

> [!NOTE]
>
> 在用于基因组学的 Databricks Runtime 7.3 及更高版本中，将管道从 TNSeq 更名为 MutSeq。

### <a name="mutseq-pipeline-notebook"></a>MutSeq 管道笔记本

[获取笔记本](../../../_static/notebooks/genomics/mutseq-pipeline.html)

### <a name="tnseq-pipeline-notebook-legacy"></a>TNSeq 管道笔记本（旧版）

[获取笔记本](../../../_static/notebooks/genomics/tnseq-pipeline.html)