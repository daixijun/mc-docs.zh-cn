---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 07/02/2020
title: DNASeq 管道 - Azure Databricks
description: 了解如何使用用于基因组学的 Databricks Runtime 中包含的 DNASeq 管道。
ms.openlocfilehash: 33b25754f68404a780510235d0eda9f9f3629ed8
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589703"
---
# <a name="dnaseq-pipeline"></a>DNASeq 管道

> [!NOTE]
>
> 以下库版本已打包到用于基因组学的 Databricks Runtime 7.0 中。 对于用于基因组学的 Databricks Runtime 的较低版本中包含的库，请参阅[发行说明](../../../release-notes/runtime/releases.md)。

Azure Databricks DNASeq 管道是符合 [GATK 最佳做法](https://software.broadinstitute.org/gatk/best-practices/workflow?id=11145)的管道，用于短读比对，变体识别和变体批注。 它使用通过 Spark 进行并行化的以下软件包。

* BWA v0.7.17
* ADAM v0.32.0
* GATK HaplotypeCaller v4.1.4.1
* SnpEff v4.3

若要详细了解管道实现以及各种选项组合的预期运行时间和开销，请参阅[在 Scala 中构建最快的 DNASeq 管道](https://databricks.com/blog/2018/09/10/building-the-fastest-dnaseq-pipeline-at-scale.html)。

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
* 该任务应当是此页面底部提供的 DNASeq 笔记本。
* 为了获得最佳性能，请使用至少具有 60GB 内存的计算优化 VM。 我们建议使用 Standard_F32s_v2 VM。
* 如果正在运行基本质量分数校准，请改用常规用途 (**Standard_D32s_v3**) 实例，因为此操作需要更多内存。

## <a name="parameters"></a>参数

管道接受控制其行为的参数。 这里记录了最重要且最常更改的参数；其余参数可以在 DNASeq 笔记本中找到。
可以为所有运行或单次运行设置参数。

| 参数                         | 默认                           | 描述                                                                                                                                                                                                                                                                      |
|-----------------------------------|-----------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| manifest                          | 不适用                               | 描述输入的清单。                                                                                                                                                                                                                                               |
| 输出                            | 不适用                               | 应将管道输出写入到的路径。                                                                                                                                                                                                                                |
| replayMode                        | skip                              | 下列其中一项：<br><br>* `skip`：如果输出已存在，则跳过相关阶段。<br>* `overwrite`：将删除现有输出。                                                                                                                                                              |
| exportVCF                         | false                             | 如果为 true，则管道会将结果写入 VCF 以及 Delta Lake。                                                                                                                                                                                                               |
| referenceConfidenceMode           | 无                              | 下列其中一项：<br><br>* 如果为 `NONE`，则输出中将仅包含变体站点<br>* 如果为 `GVCF`，则会包含所有站点，其中包含相邻的参考站点。<br>* 如果为 `BP_RESOLUTION`，则会包含所有站点。                                                                         |
| perSampleTimeout                  | 12h                               | 每个样本所应用的超时。 达到此超时时间后，管道会继续执行下一个样本。 此参数的值必须包含超时单位：“s”（表示秒）、“m”（表示分钟）或“h”（表示小时）。 例如，“60m”会导致超时 60 分钟。 |

> [!TIP]
>
> 若要优化运行时间，请将 Spark 配置中的 `spark.sql.shuffle.partitions` 设置为群集的核心数的三倍。

## <a name="customization"></a>自定义

你可以通过禁用读取比对、变体识别和变体批注来自定义 DNASeq 管道。 默认情况下会启用所有三个阶段。

```scala
val pipeline = new DNASeqPipeline(align = true, callVariants = true, annotate = true)
```

若要禁用变体批注，请设置管道，如下所示：

```scala
val pipeline = new DNASeqPipeline(align = true, callVariants = true, annotate = false)
```

允许的阶段组合如下：

| 读取比对 | 变体识别 | 变体批注 |
|----------------|-----------------|--------------------|
| true           | true            | true               |
| true           | true            | false              |
| true           | false           | false              |
| false          | true            | true               |
| false          | true            | false              |

## <a name="reference-genomes"></a>参考基因组

必须使用一个[环境变量](../../../clusters/configure.md#environment-variables)来配置参考基因组。
若要使用 GRCh37，请设置环境变量，如下所示：

```ini
refGenomeId=grch37
```

若要改用 GRCh38，请将 `grch37` 替换为 `grch38`。

### <a name="custom-reference-genomes"></a>自定义参考基因组

> [!NOTE]
>
> 用于基因组学的 Databricks Runtime 6.6 及更高版本支持自定义参考基因组。

若要使用 GRCh37 或 GRCh38 以外的参考生成，请执行以下步骤：

1. [准备参考](https://gatkforums.broadinstitute.org/gatk/discussion/2798/howto-prepare-a-reference-for-use-with-bwa-and-gatk)，以便将其与 BWA 和 GATK 一起使用。

   参考基因组目录内容应包含以下文件：

   ```
   <reference_name>.dict
   <reference_name>.fa
   <reference_name>.fa.amb
   <reference_name>.fa.ann
   <reference_name>.fa.bwt
   <reference_name>.fa.fai
   <reference_name>.fa.pac
   <reference_name>.fa.sa
   ```

2. 将参考基因组文件上传到云存储或 DBFS 中的目录。 如果你将文件上传到云存储，则必须[将目录装载到 DBFS 中的某个位置](../../../data/databricks-file-system.md#mount-storage)。
3. 在群集配置中，将[环境变量](../../../clusters/configure.md#environment-variables) `REF_GENOME_PATH` 设置为指向 DBFS 中 fasta 文件的路径。 例如，

   ```ini
   REF_GENOME_PATH=/mnt/reference-genome/reference.fa
   ```

   路径不得包含 `dbfs:` 前缀。

   使用自定义参考基因组时，将跳过 SnpEff 批注阶段。

> [!TIP]
>
> 在群集初始化期间，Azure Databricks DNASeq 管道使用提供的 BWA 索引文件来生成索引映像文件。 如果计划多次使用相同的参考基因组，可以提前生成索引映像文件来加速群集启动。 此过程会将群集启动时间减少大约 30 秒。
>
> 1. 将参考基因组目录复制到用于基因组学的 Databricks Runtime 群集的驱动程序节点。
>
>    ```bash
>    %sh cp -r /dbfs/<reference_dir_path> /local_disk0/reference-genome
>    ```
>
> 2. 从 BWA 索引文件生成索引映像文件。
>
>    ```scala
>    import org.broadinstitute.hellbender.utils.bwa._
>    BwaMemIndex.createIndexImageFromIndexFiles("/local_disk0/reference-genome/<reference_name>.fa", "/local_disk0/reference-genome/<reference_name>.fa.img")
>    ```
>
> 3. 将索引映像文件复制到参考 fasta 文件所在的目录。
>
>    ```bash
>    %sh cp /local_disk0/reference-genome/<reference_name>.fa.img /dbfs/<reference_dir_path>
>    ```
>
> 4. 从 DBFS 中删除不需要的 BWA 索引文件（`.amb`、`.ann`、`.bwt`、`.pac`、`.sa`）。
>
>    ```bash
>    %fs rm <file>
>    ```

## <a name="manifest-format"></a>清单格式

> [!NOTE]
>
> 用于基因组学的 Databricks Runtime 6.6 及更高版本支持清单 blob。

清单是一个 CSV 文件或 blob，用于描述在何处查找输入 FASTQ 或 BAM 文件。 示例：

```
file_path,sample_id,paired_end,read_group_id
*_R1_*.fastq.bgz,HG001,1,read_group
*_R2_*.fastq.bgz,HG001,2,read_group
```

如果输入包含未比对的 BAM 文件，则应省略 `paired_end` 字段：

```
file_path,sample_id,paired_end,read_group_id
*.bam,HG001,,read_group
```

> [!TIP]
>
> 如果提供的清单是一个文件，则每行中的 `file_path` 字段可以是绝对路径或相对于清单文件的路径。 如果提供的清单是 blob，则 `file_path` 字段必须是绝对路径。 可以包含 glob `(*)`，以匹配许多文件。

## <a name="supported-input-formats"></a>支持的输入格式

* SAM
* BAM
* CRAM
* Parquet
* FASTQ
  * bgzip `*.fastq.bgz`（建议）：具有 `*.fastq.gz` 扩展名的 bgzip 压缩文件会被识别为 `bgz`。
  * 未压缩的 `*.fastq`
  * gzip `*.fastq.gz`

> [!IMPORTANT]
>
> Gzip 压缩的文件不可拆分。 选择自动缩放群集，将这些文件的成本降至最低。

若要对 FASTQ 进行块压缩，请安装 [htslib](https://www.htslib.org/download/)，其中包括 `bgzip` 可执行文件。

## <a name="output"></a>输出

如果启用了相应的阶段，则比对的读取（称为变体）和带批注的变体将全部写出到提供的输出目录内的 Delta 表中。 每个表按示例 ID 进行分区。 此外，如果你将管道配置为导出 BAM 或 VCF，则这些变体也会显示在输出目录下。

```
|---alignments
    |---sampleId=HG001
        |---Parquet files
|---alignments.bam
    |---HG001.bam
|---annotations
    |---Delta files
|---annotations.vcf
    |---HG001.vcf
|---genotypes
    |---Delta files
|---genotypes.vcf
    |---HG001.vcf
```

当你在新的示例上运行管道时，它将显示为新的分区。 如果为已显示在输出目录中的示例运行管道，则会覆盖该分区。

由于所有信息都在 Delta Lake 中提供，因此你可以在 Python、R、Scala 或 SQL 中使用 Spark 轻松对其进行分析。 例如： 。

### <a name="python"></a>Python

```python
# Load the data
df = spark.read.format("delta").load("/genomics/output_dir/genotypes")
# Show all variants from chromosome 12
display(df.where("contigName == '12'").orderBy("sampleId", "start"))
```

### <a name="sql"></a>SQL

```sql
-- Register the table in the catalog
CREATE TABLE genotypes
USING delta
LOCATION '/genomics/output_dir/genotypes'
```

## <a name="troubleshooting"></a>疑难解答

**作业运行缓慢，并且正在运行的任务很少**

通常指示输入 FASTQ 文件是采用 `gzip` 压缩的，而不是采用 `bgzip` 压缩的。 Gzip 压缩的文件不可拆分，因此不能并行处理输入。 请尝试使用[并行 bgzip 笔记本](../../../_static/notebooks/genomics/parallel_bgzip.html)，以便将文件目录从 `gzip` 并行转换为 `bgzip`。

## <a name="run-programmatically"></a>以编程方式运行

除了使用 UI 外，还可以使用 [Databricks CLI](../../../dev-tools/cli/index.md) 以编程方式启动管道的运行。

> [!div class="mx-imgBorder"]
> ![查找作业 ID](../../../_static/images/hls/job-id.png)

在 UI 中设置管道作业后，请复制 **作业 ID** 并将其传递给 `jobs run-now` CLI 命令。

下面是一个示例 bash 脚本，你可以针对你的工作流对其进行修改：

```bash
# Generate a manifest file
cat <<HERE >manifest.csv
file_path,sample_id,paired_end,read_group_id
dbfs:/genomics/my_new_sample/*_R1_*.fastq.bgz,my_new_sample,1,read_group
dbfs:/genomics/my_new_sample/*_R2_*.fastq.bgz,my_new_sample,2,read_group
HERE

# Upload the file to DBFS
DBFS_PATH=dbfs:/genomics/manifests/$(date +"%Y-%m-%dT%H-%M-%S")-manifest.csv
databricks fs cp manifest.csv $DBFS_PATH

# Start a new run
databricks jobs run-now --job-id <job-id> --notebook-params "{\"manifest\": \"$DBFS_PATH\"}"
```

除了从命令行启动运行外，你还可以使用此模式从自动化系统（例如 Jenkins）中调用管道。

### <a name="dnaseq-pipeline-notebook"></a>DNASeq 管道笔记本

[获取笔记本](../../../_static/notebooks/genomics/dnaseq-pipeline.html)