---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 09/03/2020
title: 预先打包的 VEP 批注管道 - Azure Databricks
description: 了解如何使用用于基因组学的 Databricks Runtime 中包含的 VEP 批注管道。
ms.openlocfilehash: 01193dce47f7805ac674f0c29a99c751bf90d743
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589699"
---
# <a name="pre-packaged-vep-annotation-pipeline"></a><a id="pre-packaged-vep-annotation-pipeline"></a><a id="vep-pipeline"></a>预先打包的 VEP 批注管道

## <a name="setup"></a>设置

将[VEP](https://uswest.ensembl.org/info/docs/tools/vep/index.html)（版本 96）作为 Azure Databricks 作业运行。 必要的详细信息如下：

* 群集配置
  * [用于基因组学的 Databricks Runtime](../../../runtime/genomicsruntime.md#dbr-genomics)
  * 为了获得最佳性能，请将 [Spark 配置](../../../clusters/configure.md#spark-config)设置为 `spark.executor.cores 1`，并使用具有至少 200GB 内存的内存优化实例。 建议使用 Standard_L32s VM。
* 将任务设置为导入工作区的 [VEPPipeline 笔记本](#vep-notebook)。

## <a name="parameters"></a>参数

管道接受多个控制其行为的参数。
所有参数都可以为所有运行或每个运行设置。

| 参数                         | 默认                                           | 描述                                                                                                                                                                                                                                                                                                                                                                          |
|-----------------------------------|---------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| inputVcf                          | 不适用                                               | 要用 VEP 批注的 VCF 文件的路径。                                                                                                                                                                                                                                                                                                                                           |
| 输出                            | 不适用                                               | 应将管道输出写入到的路径。                                                                                                                                                                                                                                                                                                                                        |
| replayMode                        | skip                                              | 下列其中一项：<br><br>* `skip`：如果输出已存在，则跳过相关阶段。<br>* `overwrite`：将删除现有输出。                                                                                                                                                                                                                                                                 |
| exportVCF                         | false                                             | 如果为 true，则管道会将结果同时写入 VCF 和 Delta Lake。                                                                                                                                                                                                                                                                                                                         |
| extraVepOptions                   | `--everything --minimal --allele_number --fork 4` | 要传递给 VEP 的其他命令行选项。 有些选项是由管道设置的，不能被重写：`--assembly`、`--cache`、`--dir_cache`、`--fasta`、`--format`、`--merged`、`--no_stats`、`--offline`、`--output_file`、`--refseq`、`--vcf`。 查看 [VEP 站点](https://uswest.ensembl.org/info/docs/tools/vep/script/vep_options.html#basic)上的所有可能选项。 |

此外，必须使用[环境变量](../../../clusters/configure.md#environment-variables)来配置引用基因组和脚本。
要将 Grch37 与合并的 Ensembl 和 RefSeq 脚本配合使用，请设置以下环境变量：

```ini
refGenomeId=grch37_merged_vep_96
```

所有引用基因组和脚本的 `refGenomeId` 如下：

|                 | grch37                   | grch38                   |
|-----------------|------------------------------|------------------------------|
| Ensembl     | `grch37_vep_96`              | `grch38_vep_96`              |
| RefSeq      | `grch37_refseq_vep_96`       | `grch38_refseq_vep_96`       |
| **已合并**      | `grch37_merged_vep_96`       | `grch38_merged_vep_96`       |

## <a name="loftee"></a>LOFTEE

可以通过插件运行 VEP，以便扩展、筛选或操作 VEP 输出。
根据所需的引用基因组，按照以下步骤设置 [LOFTEE](https://github.com/konradjk/loftee)。

### <a name="grch37"></a>grch37

使用[初始化脚本](../../../clusters/init-scripts.md#cluster-scoped-init-script)创建 LOFTEE 群集。

```bash
#!/bin/bash
DIR_VEP_PLUGINS=/opt/vep/Plugins
mkdir -p $DIR_VEP_PLUGINS
cd $DIR_VEP_PLUGINS
echo export PERL5LIB=$PERL5LIB:$DIR_VEP_PLUGINS/loftee >> /databricks/spark/conf/spark-env.sh
git clone --depth 1 --branch master https://github.com/konradjk/loftee.git
```

建议创建一个装入点来存储云存储中的任何其他文件；然后可以使用 [FUSE 装载](../../../data/databricks-file-system.md#fuse)访问这些文件。
将脚本中的值替换为你的装入点。

如果需要，请将祖先序列保存在装入点。

```bash
cd <mount-point>
wget https://s3.amazonaws.com/bcbio_nextgen/human_ancestor.fa.gz
wget https://s3.amazonaws.com/bcbio_nextgen/human_ancestor.fa.gz.fai
wget https://s3.amazonaws.com/bcbio_nextgen/human_ancestor.fa.gz.gzi
```

如果需要，请将 PhyloCSF 数据库保存在装入点。

```bash
cd <mount-point>
wget https://personal.broadinstitute.org/konradk/loftee_data/GRCh37/phylocsf_gerp.sql.gz
gunzip phylocsf_gerp.sql.gz
```

运行 VEP 管道时，请提供相应的附加选项。

```bash
--dir_plugins /opt/vep/Plugins --plugin LoF,loftee_path:/opt/vep/Plugins/loftee,human_ancestor_fa:<mount-point>/human_ancestor.fa.gz,conservation_file:<mount-point>/phylocsf_gerp.sql
```

### <a name="grch38"></a>grch38

创建一个可以使用[初始化脚本](../../../clusters/init-scripts.md#cluster-scoped-init-script)分析 BigWig 文件的 LOFTEE 群集。

```bash
#!/bin/bash

# Download LOFTEE
DIR_VEP_PLUGINS=/opt/vep/Plugins
mkdir -p $DIR_VEP_PLUGINS
cd $DIR_VEP_PLUGINS
echo export PERL5LIB=$PERL5LIB:$DIR_VEP_PLUGINS/loftee >> /databricks/spark/conf/spark-env.sh
git clone --depth 1 --branch grch38 https://github.com/konradjk/loftee.git

# Download Kent source tree
mkdir -p /tmp/bigfile
cd /tmp/bigfile
wget https://github.com/ucscGenomeBrowser/kent/archive/v335_base.tar.gz
tar xzf v335_base.tar.gz

# Build Kent source
export KENT_SRC=$PWD/kent-335_base/src
export MACHTYPE=$(uname -m)
export CFLAGS="-fPIC"
export MYSQLINC=`mysql_config --include | sed -e 's/^-I//g'`
export MYSQLLIBS=`mysql_config --libs`
cd $KENT_SRC/lib
echo 'CFLAGS="-fPIC"' > ../inc/localEnvironment.mk
make clean
make
cd ../jkOwnLib
make clean
make

# Install Bio::DB::BigFile
cpanm --notest Bio::Perl
cpanm --notest Bio::DB::BigFile
```

建议创建一个装入点来存储云存储中的任何其他文件；然后可以使用 [FUSE 装载](../../../data/databricks-file-system.md#fuse)访问这些文件。
将脚本中的值替换为你的装入点。

将 GERP 评分 BigWig 保存在装入点。

```bash
cd <mount-point>
wget https://personal.broadinstitute.org/konradk/loftee_data/GRCh38/gerp_conservation_scores.homo_sapiens.GRCh38.bw
```

如果需要，请将祖先序列保存在装入点。

```bash
cd <mount-point>
wget https://personal.broadinstitute.org/konradk/loftee_data/GRCh38/human_ancestor.fa.gz
wget https://personal.broadinstitute.org/konradk/loftee_data/GRCh38/human_ancestor.fa.gz.fai
wget https://personal.broadinstitute.org/konradk/loftee_data/GRCh38/human_ancestor.fa.gz.gzi
```

如果需要，请将 PhyloCSF 数据库保存在装入点。

```bash
cd <mount-point>
wget https://personal.broadinstitute.org/konradk/loftee_data/GRCh38/loftee.sql.gz
gunzip loftee.sql.gz
```

运行 VEP 管道时，请提供相应的附加选项。

```bash
--dir_plugins /opt/vep/Plugins --plugin LoF,loftee_path:/opt/vep/Plugins/loftee,gerp_bigwig:<mount-point>/gerp_conservation_scores.homo_sapiens.GRCh38.bw,human_ancestor_fa:<mount-point>/human_ancestor.fa.gz,conservation_file:<mount-point>/loftee.sql
```

#### <a name="vep-pipeline-notebook"></a><a id="vep-notebook"></a><a id="vep-pipeline-notebook"></a>VEP 管道笔记本

[获取笔记本](../../../_static/notebooks/genomics/vep-pipeline.html)