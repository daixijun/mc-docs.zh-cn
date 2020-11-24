---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 07/02/2020
title: RNASeq 管道 - Azure Databricks
description: 了解如何使用用于基因组学的 Databricks Runtime 中包含的 RNASeq 管道。
ms.openlocfilehash: 3b72ba6255724b91db8c6b9d6402f7df467ada52
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589702"
---
# <a name="rnaseq-pipeline"></a>RNASeq 管道

> [!NOTE]
>
> 以下库版本已打包到用于基因组学的 Databricks Runtime 7.0 中。 对于用于基因组学的 Databricks Runtime 的较低版本中包含的库，请参阅[发行说明](../../../release-notes/runtime/releases.md)。

Databricks RNASeq 管道使用 [STAR](https://github.com/alexdobin/STAR) v2.6.1a 和 [ADAM](https://adam.readthedocs.io/en/latest/) v0.32.0 处理短读取比对和量化。

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
    "defaultValue": "grch38_star"
  },
  "spark_version": {
    "type": "regex",
    "pattern": ".*-hls.*",
    "defaultValue": "7.0.x-hls-scala2.12"
  }
}
```

* 该任务应为此页底部提供的 RNASeq 笔记本。
* 为了获得最佳性能，请使用至少具有 60GB 内存的计算优化 VM。 我们建议使用 Standard_F32s_v2 VM。

## <a name="parameters"></a>参数

管道接受多个控制其行为的参数。 这里记录了最重要且最常更改的参数；其余参数可以在 RNASeq 笔记本中找到。 可以为所有运行或单次运行设置所有参数。

| 参数                         | 默认                           | 描述                                                                                                                                                                                                                                                                      |
|-----------------------------------|-----------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| manifest                          | 不适用                               | 描述输入的清单。                                                                                                                                                                                                                                               |
| 输出                            | 不适用                               | 应将管道输出写入到的路径。                                                                                                                                                                                                                                |
| replayMode                        | skip                              | 下列其中一项：<br><br>* `skip`：如果输出已存在，则跳过相关阶段。<br>* `overwrite`：将删除现有输出。                                                                                                                                                              |
| perSampleTimeout                  | 12 小时                               | 每个样本应用的超时。 达到此超时时间后，管道会继续执行下一个样本。 此参数的值必须包含超时单位：“s”（表示秒）、“m”（表示分钟）或“h”（表示小时）。 例如，“60m”会导致超时 60 分钟。 |

此外，必须使用[环境变量](../../../clusters/configure.md#environment-variables)来配置参考基因组。
若要使用 Grch37，请设置环境变量：

```ini
refGenomeId=grch37_star
```

若要改用 Grch38，请按如下所示设置环境变量：

```ini
refGenomeId=grch38_star
```

## <a name="walkthrough"></a>演练

此管道包含两个步骤：

1. 比对：使用 STAR 比对器将每个短读取映射到参考基因组。
2. 量化：计算与每个参考脚本对应的读取数。

## <a name="additional-usage-info-and-troubleshooting"></a>其他使用信息和故障排除

RNASeq 管道在操作方面与 DNASeq 管道非常相似。 有关清单格式、输出结构、编程用法以及常见问题的详细信息，请参阅 [DNASeq 管道](dnaseq-pipeline.md)。

### <a name="rnaseq-pipeline-notebook"></a>RNASeq 管道笔记本

[获取笔记本](../../../_static/notebooks/genomics/rnaseq-pipeline.html)