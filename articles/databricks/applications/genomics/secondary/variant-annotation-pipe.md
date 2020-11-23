---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 05/07/2020
title: 使用管道转换器进行突变注释 - Azure Databricks
description: 使用 Glow 的管道转换器了解突变注释。
ms.openlocfilehash: 3d3b81e0f8ea459dd3cf2285f2ce61326030f9d6
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589698"
---
# <a name="variant-annotation-using-pipe-transformer"></a><a id="variant-annotation-pipe"> </a><a id="variant-annotation-using-pipe-transformer"> </a>使用管道转换器进行突变注释

任何注释方法均可用于使用 Glow 的管道转换器的突变数据。

例如，通过以下方式执行 VEP 注释：将注释数据源（[缓存](https://uswest.ensembl.org/info/docs/tools/vep/script/vep_cache.html)）下载到群集中的每个节点，并使用与以下单元类似的脚本通过管道转换器调用 [VEP 命令行脚本](https://github.com/Ensembl/ensembl-vep)。

```python
import glow
import json

input_vcf = "/databricks-datasets/hail/data-001/1kg_sample.vcf.bgz"
input_df = spark.read.format("vcf").load(input_vcf)
cmd = json.dumps([
  "/opt/vep/src/ensembl-vep/vep",
  "--dir_cache", "/mnt/dbnucleus/dbgenomics/grch37_merged_vep_96",
  "--fasta", "/mnt/dbnucleus/dbgenomics/grch37_merged_vep_96/data/human_g1k_v37.fa",
  "--assembly", "GRCh37",
  "--format", "vcf",
  "--output_file", "STDOUT",
  "--no_stats",
  "--cache",
  "--offline",
  "--vcf",
  "--merged"])
output_df = glow.transform("pipe", input_df, cmd=cmd, input_formatter='vcf', in_vcf_header=input_vcf, output_formatter='vcf')
output_df.write.format("delta").save("dbfs:/mnt/vep-pipe")
```