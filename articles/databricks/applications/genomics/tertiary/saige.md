---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/04/2020
title: SAIGE 和 SAIGE-GENE（使用管道转换器）- Azure Databricks
description: 了解如何使用包含在用于基因组学的 Databricks Runtime 中的 SAIGE 和 SAIGE-GENE。
ms.openlocfilehash: 7b7d5aca630e8e06b5ba2dc1610c88586c6c93d1
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589684"
---
# <a name="saige-and-saige-gene-using-pipe-transformer"></a><a id="saige"> </a><a id="saige-and-saige-gene-using-pipe-transformer"> </a>SAIGE 和 SAIGE-GENE（使用管道转换器）

[SAIGE](https://github.com/weizhouUMICH/SAIGE) 是一种使用 R 语言编写的命令行工具，用于对生物样本库规模的数据执行遗传关联测试。
使用 Glow 的管道转换器，运行[用于基因组学的 Databricks Runtime](../../../runtime/genomicsruntime.md#dbr-genomics)上的[单变量关联测试方法 SAIGE](https://www.nature.com/articles/s41588-018-0184-y) 和[基于区域的关联测试方法 SAIGE-GENE](https://www.biorxiv.org/content/10.1101/583278v1)。

## <a name="create-a-saige-cluster"></a><a id="create-a-saige-cluster"> </a><a id="saige-init-script"> </a>创建 SAIGE 群集

使用[初始化脚本](../../../clusters/init-scripts.md#cluster-scoped-init-script)，在群集中的每个节点上安装 [HTSLib](https://github.com/samtools/htslib) 以及 SAIGE 包、其依赖项和关联的输入文件。
建议创建一个装入点，通过 [FUSE 装载](../../../data/databricks-file-system.md#fuse)来存储和下载云存储中的这些文件。
将脚本中的值替换为你的装入点和本地目录。

```bash
#!/usr/bin/env bash

set -ex
set -o pipefail

# Install SKAT for SAIGE-GENE
sudo apt-get -y install libboost-all-dev autoconf
Rscript -e 'install.packages("SKAT", repos="https://cran.us.r-project.org")'

# Install SAIGE
cd /opt
git clone https://github.com/weizhouUMICH/SAIGE
cd SAIGE
Rscript extdata/install_packages.R
R CMD INSTALL *.tar.gz

# Install HTSLIB
cd /opt
git clone https://github.com/samtools/htslib
cd htslib
autoheader
autoconf
./configure
make
make install

# Sync SAIGE input files
cp -r <mount-point>/* <local-dir>
```

## <a name="run-association-tests"></a>运行关联测试

若要使关联测试并行化，请使用管道转换器。

* 命令：一个 JSON 数组，其中包含 [SAIGE](#saige-cmd) 或 [SAIGE-GENE](#saige-gene-cmd) 脚本
* 输入格式化程序：VCF
* 输出格式化程序：CSV
  * 标头标志：true
  * 分隔符：空格

```python
import glow
import json

cmd = json.dumps(["bash", "-c", saige_script])
output_df = glow.transform("pipe", input_df, cmd=cmd, input_formatter='vcf', in_vcf_header=input_vcf,
                           output_formatter='csv', out_header='true', out_delimiter=' ')
```

可以直接从 VCF 或带有 VCF 行的 Delta 表中读取输入数据帧。

### <a name="single-variant-association-test-saige"></a>单变量关联测试 (SAIGE)

以下单元格显示要通过管道传输的示例 SAIGE 脚本。
[单变量关联测试文档](https://github.com/weizhouUMICH/SAIGE/wiki/Genetic-association-tests-using-SAIGE#step-2-performing-the-single-variant-association-tests)中介绍了这些选项。

```bash
#!/bin/sh
set -e

export tmpdir=$(mktemp -d -t vcf.XXXXXX)
cat - > ${tmpdir}/input.vcf
bgzip -c ${tmpdir}/input.vcf > ${tmpdir}/input.vcf.gz
tabix -p vcf ${tmpdir}/input.vcf.gz

Rscript /opt/SAIGE/extdata/step2_SPAtests.R \
    --vcfFile=${tmpdir}/input.vcf.gz \
    --vcfFileIndex=${tmpdir}/input.vcf.gz.tbi \
    --SAIGEOutputFile=${tmpdir}/output.txt \
    --sampleFile=<local-dir>/input/sampleIds.txt \
    --GMMATmodelFile=<local-dir>/step-1/output.rda \
    --varianceRatioFile=<local-dir>/step-1/output.varianceRatio.txt \
    --vcfField=GT \
    --chrom=22 \
    --minMAF=0.0001 \
    --minMAC=1 \
    --numLinesOutput=2 \
    >&2

cat ${tmpdir}/output.txt
rm -rf ${tmpdir}
```

### <a name="region-based-association-test-saige-gene"></a><a id="region-based-association-test-saige-gene"> </a><a id="saige-cmd"> </a>基于区域的关联测试 (SAIGE-GENE)

在进行管道传输前，按区域或基因对输入数据帧进行分区，每个分区包含已排序且不同的相关 VCF 行集。 区域和对应的变量应与 SAIGE-GENE 脚本中引用的组文件中的区域和对应的变量匹配。 区域可从任何源派生，例如 [SnpEff 管道](../secondary/snpeff-pipeline.md#snpeff-pipeline)中的基因注释。

```python
input_df = genotype_df.join(region_df, ["contigName", "start", "end"]) \
  .repartition("geneName") \
  .select("contigName", "start", "end", "referenceAllele", "alternateAlleles", "genotypes") \
  .sortWithinPartitions("contigName", "start")
```

以下单元格显示了一个要通过管道传输的示例 SAIGE-GENE 脚本。
[基于区域的关联测试文档](https://github.com/weizhouUMICH/SAIGE/wiki/Genetic-association-tests-using-SAIGE#step-2-performing-the-region--or-gene-based-association-tests)中介绍了这些选项。

```bash
#!/bin/sh
set -e

export tmpdir=$(mktemp -d -t vcf.XXXXXX)
uniq -w 100 - > ${tmpdir}/input.vcf
bgzip -c ${tmpdir}/input.vcf > ${tmpdir}/input.vcf.gz
tabix -p vcf ${tmpdir}/input.vcf.gz

Rscript /opt/SAIGE/extdata/step2_SPAtests.R \
    --vcfFile=${tmpdir}/input.vcf.gz \
    --vcfFileIndex=${tmpdir}/input.vcf.gz.tbi \
    --SAIGEOutputFile=${tmpdir}/output.txt \
    --groupFile=${tmpdir}/groupFile.txt \
    --sampleFile=<local-dir>/input/sampleIds.txt \
    --GMMATmodelFile=<local-dir>/step-1/output.rda \
    --varianceRatioFile=<local-dir>/step-1/output.varianceRatio.txt \
    --sparseSigmaFile=<local-dir>/step-1/output.varianceRatio.txt_relatednessCutoff_0.125_2000_randomMarkersUsed.sparseSigma.mtx \
    --vcfField=GT \
    --chrom=22 \
    --minMAF=0 \
    --minMAC=0.5 \
    --maxMAFforGroupTest=0.5 \
    --numLinesOutput=2 \
    >&2

cat ${tmpdir}/output.txt
rm -rf ${tmpdir}
```

在进行管道传输后，消除输出数据帧中每个基因的关联结果的歧义。

```python
from pyspark.sql import Window

window_spec = Window.partitionBy("Gene")
assoc_df = output_df.withColumn("numMarkerIDs", size(split("markerIDs", ";"))) \
  .withColumn("maxNumMarkerIDs", max("numMarkerIDs").over(window_spec)) \
  .filter("numMarkerIDs = maxNumMarkerIDs") \
  .drop("numMarkerIDs", "maxNumMarkerIDs") \
  .drop_duplicates(["Gene"])
```

## <a name="example-notebooks"></a><a id="example-notebooks"> </a><a id="saige-gene-cmd"> </a>示例笔记本

### <a name="null-logistic-mixed-model-fit-notebook"></a>NULL 逻辑混合模型拟合笔记本

[获取笔记本](../../../_static/notebooks/genomics/null-fit.html)

### <a name="saige-notebook"></a>SAIGE 笔记本

[获取笔记本](../../../_static/notebooks/genomics/saige.html)

### <a name="saige-gene-notebook"></a>SAIGE-GENE 笔记本

[获取笔记本](../../../_static/notebooks/genomics/saige-gene.html)