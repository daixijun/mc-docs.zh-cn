---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/03/2020
title: 如何使用分区修剪提高 Delta Lake MERGE INTO 查询的性能 - Azure Databricks
description: 了解如何使用分区修剪提高 Delta Lake MERGE INTO 查询的性能。
category: Delta
ms.openlocfilehash: cf9adac2ec496412ad4a9fb879e9ad287d6419f1
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589554"
---
# <a name="how-to-improve-performance-of-delta-lake-merge-into-queries-using-partition-pruning"></a>如何使用分区修剪提高 Delta Lake `MERGE INTO` 查询的性能

本文介绍如何在从 Azure Databricks 发出的 Delta Lake [MERGE INTO](/databricks/spark/latest/spark-sql/language-manual/merge-into) 查询中触发分区修剪。

分区修剪是一种优化技术，用于限制由查询检查的分区数。

## <a name="discussion"></a>讨论 (Discussion)

与 Delta 表一起使用时，`MERGE INTO` 是一项高成本操作。 如果未对基础数据进行分区并适当使用它，则查询性能可能会受到严重影响。

本课程的主要内容是：如果知道 `MERGE INTO` 查询需要检查哪些分区，则应在查询中指定这些分区，以便执行分区修剪。

## <a name="demonstration-no-partition-pruning"></a>演示：无分区修剪

下面的示例展示了未进行分区修剪的性能不佳的 `MERGE INTO` 查询。

首先创建以下名为 `delta_merge_into` 的 Delta 表：

```scala
val df = spark.range(30000000)
    .withColumn("par", ($"id" % 1000).cast(IntegerType))
    .withColumn("ts", current_timestamp())
    .write
      .format("delta")
      .mode("overwrite")
      .partitionBy("par")
      .saveAsTable("delta_merge_into")
```

然后，将数据帧合并到 Delta 表，以创建名为 `update` 的表：

```scala
val updatesTableName = "update"
val targetTableName = "delta_merge_into"
val updates = spark.range(100).withColumn("id", (rand() * 30000000 * 2).cast(IntegerType))
    .withColumn("par", ($"id" % 2).cast(IntegerType))
    .withColumn("ts", current_timestamp())
    .dropDuplicates("id")
updates.createOrReplaceTempView(updatesTableName)
```

`update` 表有 100 行以及 `id`、`par` 和 `ts` 这三列。 `par` 的值始终为 1 或 0。

假设你运行下面这个简单的 `MERGE INTO` 查询：

```scala
spark.sql(s"""
    |MERGE INTO $targetTableName
    |USING $updatesTableName
     |ON $targetTableName.id = $updatesTableName.id
     |WHEN MATCHED THEN
     |  UPDATE SET $targetTableName.ts = $updatesTableName.ts
    |WHEN NOT MATCHED THEN
    |  INSERT (id, par, ts) VALUES ($updatesTableName.id, $updatesTableName.par, $updatesTableName.ts)
 """.stripMargin)
```

该查询需要 13.16 分钟才能完成：

> [!div class="mx-imgBorder"]
> ![no-alternative-text](../_static/images/delta/without-partition-filters.png)

此查询的物理计划包含 `PartitionCount: 1000`，如下所示。 这意味着 Apache Spark 要扫描完 1000 个分区才会执行查询。 这并不是一种有效的查询，因为 `update` 数据仅具有分区值 `1` 和 `0`：

```
== Physical Plan ==
*(5) HashAggregate(keys=[], functions=[finalmerge_count(merge count#8452L) AS count(1)#8448L], output=[count#8449L])
+- Exchange SinglePartition
   +- *(4) HashAggregate(keys=[], functions=[partial_count(1) AS count#8452L], output=[count#8452L])
    +- *(4) Project
       +- *(4) Filter (isnotnull(count#8440L) && (count#8440L > 1))
          +- *(4) HashAggregate(keys=[_row_id_#8399L], functions=[finalmerge_sum(merge sum#8454L) AS sum(cast(one#8434 as bigint))#8439L], output=[count#8440L])
             +- Exchange hashpartitioning(_row_id_#8399L, 200)
                +- *(3) HashAggregate(keys=[_row_id_#8399L], functions=[partial_sum(cast(one#8434 as bigint)) AS sum#8454L], output=[_row_id_#8399L, sum#8454L])
                   +- *(3) Project [_row_id_#8399L, UDF(_file_name_#8404) AS one#8434]
                      +- *(3) BroadcastHashJoin [cast(id#7514 as bigint)], [id#8390L], Inner, BuildLeft, false
                         :- BroadcastExchange HashedRelationBroadcastMode(List(cast(input[0, int, true] as bigint)))
                         :  +- *(2) HashAggregate(keys=[id#7514], functions=[], output=[id#7514])
                         :     +- Exchange hashpartitioning(id#7514, 200)
                         :        +- *(1) HashAggregate(keys=[id#7514], functions=[], output=[id#7514])
                         :           +- *(1) Filter isnotnull(id#7514)
                         :              +- *(1) Project [cast(((rand(8188829649009385616) * 3.0E7) * 2.0) as int) AS id#7514]
                         :                 +- *(1) Range (0, 100, step=1, splits=36)
                         +- *(3) Filter isnotnull(id#8390L)
                            +- *(3) Project [id#8390L, _row_id_#8399L, input_file_name() AS _file_name_#8404]
                               +- *(3) Project [id#8390L, monotonically_increasing_id() AS _row_id_#8399L]
                                  +- *(3) Project [id#8390L, par#8391, ts#8392]
                                     +- *(3) FileScan parquet [id#8390L,ts#8392,par#8391] Batched: true, DataFilters: [], Format: Parquet, Location: TahoeBatchFileIndex[dbfs:/user/hive/warehouse/delta_merge_into], PartitionCount: 1000, PartitionFilters: [], PushedFilters: [], ReadSchema: struct<id:bigint,ts:timestamp>
```

## <a name="solution"></a>解决方案

<!--There are two solutions:

* Rewrite the query to specify the partitions manually.
* Enable dynamic partition pruning.

### Manually specify partitions-->

重写查询，指定分区。

此 `MERGE INTO` 查询可直接指定分区：

```scala
spark.sql(s"""
     |MERGE INTO $targetTableName
     |USING $updatesTableName
     |ON $targetTableName.par IN (1,0) AND $targetTableName.id = $updatesTableName.id
     |WHEN MATCHED THEN
     |  UPDATE SET $targetTableName.ts = $updatesTableName.ts
     |WHEN NOT MATCHED THEN
     |  INSERT (id, par, ts) VALUES ($updatesTableName.id, $updatesTableName.par, $updatesTableName.ts)
 """.stripMargin)
```

针对同一群集，该查询现在只需 20.54 秒即可完成。

> [!div class="mx-imgBorder"]
> ![no-alternative-text](../_static/images/delta/with-partition-filters.png)

此查询的物理计划包含 `PartitionCount: 2`，如下所示。 只做了少量更改，查询现在快了 40 倍以上：

```
== Physical Plan ==
*(5) HashAggregate(keys=[], functions=[finalmerge_count(merge count#7892L) AS count(1)#7888L], output=[count#7889L])
+- Exchange SinglePartition
   +- *(4) HashAggregate(keys=[], functions=[partial_count(1) AS count#7892L], output=[count#7892L])
    +- *(4) Project
       +- *(4) Filter (isnotnull(count#7880L) && (count#7880L > 1))
          +- *(4) HashAggregate(keys=[_row_id_#7839L], functions=[finalmerge_sum(merge sum#7894L) AS sum(cast(one#7874 as bigint))#7879L], output=[count#7880L])
             +- Exchange hashpartitioning(_row_id_#7839L, 200)
                +- *(3) HashAggregate(keys=[_row_id_#7839L], functions=[partial_sum(cast(one#7874 as bigint)) AS sum#7894L], output=[_row_id_#7839L, sum#7894L])
                   +- *(3) Project [_row_id_#7839L, UDF(_file_name_#7844) AS one#7874]
                      +- *(3) BroadcastHashJoin [cast(id#7514 as bigint)], [id#7830L], Inner, BuildLeft, false
                         :- BroadcastExchange HashedRelationBroadcastMode(List(cast(input[0, int, true] as bigint)))
                         :  +- *(2) HashAggregate(keys=[id#7514], functions=[], output=[id#7514])
                         :     +- Exchange hashpartitioning(id#7514, 200)
                         :        +- *(1) HashAggregate(keys=[id#7514], functions=[], output=[id#7514])
                         :           +- *(1) Filter isnotnull(id#7514)
                         :              +- *(1) Project [cast(((rand(8188829649009385616) * 3.0E7) * 2.0) as int) AS id#7514]
                         :                 +- *(1) Range (0, 100, step=1, splits=36)
                         +- *(3) Project [id#7830L, _row_id_#7839L, _file_name_#7844]
                            +- *(3) Filter (par#7831 IN (1,0) && isnotnull(id#7830L))
                               +- *(3) Project [id#7830L, par#7831, _row_id_#7839L, input_file_name() AS _file_name_#7844]
                                  +- *(3) Project [id#7830L, par#7831, monotonically_increasing_id() AS _row_id_#7839L]
                                     +- *(3) Project [id#7830L, par#7831, ts#7832]
                                        +- *(3) FileScan parquet [id#7830L,ts#7832,par#7831] Batched: true, DataFilters: [], Format: Parquet, Location: TahoeBatchFileIndex[dbfs:/user/hive/warehouse/delta_merge_into], PartitionCount: 2, PartitionFilters: [], PushedFilters: [], ReadSchema: struct<id:bigint,ts:timestamp>
```

<!--### Dynamic partition pruning

Enable dynamic partition pruning by making the following setting in a notebook cell:

```python
spark.conf.set("spark.databricks.optimizer.dynamicPartitionPruning","true")
```

.. note::
    This operation applies to <DBR> 5.5 LTS. You will not see a performance benefit from dynamic partition pruning on <DBR> 6.0 and above.-->