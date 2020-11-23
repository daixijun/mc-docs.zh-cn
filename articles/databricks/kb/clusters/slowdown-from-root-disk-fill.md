---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
ms.date: 08/06/2020
title: 由于 Ganglia 指标填充根分区，导致群集速度缓慢 - Azure Databricks
description: 解决此问题：由于 Ganglia 指标数据爆炸，使指标填充根分区，从而导致群集速度变慢。
category: Clusters
author: kr-arjun
db-author: arjunkr@databricks.com
ms.openlocfilehash: 4a6edf00f636abc282926fe9723477202e570ffc
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589866"
---
# <a name="cluster-slowdown-due-to-ganglia-metrics-filling-root-partition"></a>由于 Ganglia 指标填充根分区，导致群集速度缓慢

## <a name="problem"></a>问题

群集的速度开始变慢，可能会显示下列多种症状：

1. 报告运行不正常的群集事件：
   * 请求已超时。驱动程序暂时不可用。
   * 元存储关闭。
   * DBFS 关闭。
2. 无法查看与驱动程序进程关联的任何高 GC 事件或内存利用率。
3. 在驱动程序节点上使用 `top` 时，你发现间歇性的高平均负载。
4. 与 Ganglia 相关的 `gmetad` 进程显示间歇性的高 CPU 利用率。
5. 根磁盘显示 `df -h /` 的高磁盘使用率。 具体而言，`/var/lib/ganglia/rrds` 显示高磁盘使用率。
6. Ganglia UI 无法显示负载分布。

可通过在 `/var/lib/ganglia/rrds` 中查找前缀中包含 `local` 的文件来验证该问题。 通常，此目录应只包含前缀为 `application-<applicationId>` 的文件。

例如：

```bash
%sh ls -ltrhR /var/lib/ganglia/rrds/  | grep -i local

rw-rw-rw- 1 ganglia ganglia 616K Jun 29 18:00 local-1593453624916.driver.Databricks.directoryCommit.markerReadErrors.count.rrd -rw-rw-rw- 1 ganglia ganglia 616K Jun 29 18:00 local-1593453614595.driver.Databricks.directoryCommit.deletedFilesFiltered.count.rrd -rw-rw-rw- 1 ganglia ganglia 616K Jun 29 18:00 local-1593453614595.driver.Databricks.directoryCommit.autoVacuumCount.count.rrd -rw-rw-rw- 1 ganglia ganglia 616K Jun 29 18:00 local-1593453605184.driver.CodeGenerator.generatedMethodSize.min.rrd
```

## <a name="cause"></a>原因

Ganglia 指标通常使用小于 10GB 的磁盘空间。 但是，在某些情况下，可能会发生“数据爆炸”，这会导致根分区被 Ganglia 指标填满。 数据爆炸还会创建一个脏缓存。 发生这种情况时，Ganglia 指标可能会占用超过 100GB 的根磁盘空间。

如果你在 Python 文件中将 `spark` 会话变量定义为全局变量，然后调用在同一文件中定义的函数来对数据执行 Apache Spark 转换，则可能会发生这种“数据爆炸”。 发生这种情况时，可将 Spark 会话逻辑与所需的函数定义一起进行序列化，从而在工作器节点上创建一个 Spark 会话。

采用以下 Spark 会话定义为例：

```python
from pyspark.sql import SparkSession

def get_spark():
    """Returns a spark session."""
    return SparkSession.builder.getOrCreate()

if "spark" not in globals():
  spark = get_spark()

def generator(partition):
    print(globals()['spark'])
    for row in partition:
        yield [word.lower() for word in row["value"]]
```

如果你使用以下示例命令，则将创建前缀为 `local` 的文件：

```python
from repro import ganglia_test
df = spark.createDataFrame([(["Hello"], ), (["Spark"], )], ["value"])
df.rdd.mapPartitions(ganglia_test.generator).toDF(["value"]).show()
```

`generator()` 函数中的 `print(globals()['spark'])` 语句不会导致错误，因为它可用作工作器节点中的全局变量。 在某些情况下，它可能会失败并出现无效键错误，因为该值不可用作全局变量。 以较短批处理时间间隔执行的流式处理作业容易出现此问题。

## <a name="solution"></a>解决方案

确保未使用 `SparkSession.builder.getOrCreate()` 将一个 Spark 会话定义为全局变量。

进行故障排除时，可使用带有 `local` 前缀的文件上的时间戳，帮助确定首次引入有问题的更改的时间。