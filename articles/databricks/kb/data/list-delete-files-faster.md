---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/10/2020
title: 如何在 Azure Databricks 中更快地列出和删除文件 - Azure Databricks
description: 了解如何在 Azure Databricks 中更快地列出和删除文件。
ms.openlocfilehash: 990c886ea0f77a00861776310bfc080f1bd1f98e
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589598"
---
# <a name="how-to-list-and-delete-files-faster-in-azure-databricks"></a>如何在 Azure Databricks 中更快地列出和删除文件

## <a name="scenario"></a>方案

假设你需要删除按 `year`、`month`、`date`、`region` 和 `service` 分区的表。 但是，这个表很大，每个分区大约有 1000 个 `part` 文件。 实现此目的的一种方法是列出每个分区中的所有文件，然后使用 Apache Spark 作业将其删除。 例如，假设有一个按 a、b 和 c 分区的表：

```scala
Seq((1,2,3,4,5),
  (2,3,4,5,6),
  (3,4,5,6,7),
  (4,5,6,7,8))
  .toDF("a", "b", "c", "d", "e")
  .write.mode("overwrite")
  .partitionBy("a", "b", "c")
  .parquet("/mnt/path/table")
```

## <a name="list-files"></a>列出文件

你可以使用此函数列出所有 part 文件：

```scala
import org.apache.hadoop.conf.Configuration
import org.apache.hadoop.fs.{Path, FileSystem}
import org.apache.spark.deploy.SparkHadoopUtil
import org.apache.spark.sql.execution.datasources.InMemoryFileIndex
import java.net.URI

def listFiles(basep: String, globp: String): Seq[String] = {
  val conf = new Configuration(sc.hadoopConfiguration)
  val fs = FileSystem.get(new URI(basep), conf)

  def validated(path: String): Path = {
    if(path startsWith "/") new Path(path)
    else new Path("/" + path)
  }

  val fileCatalog = InMemoryFileIndex.bulkListLeafFiles(
    paths = SparkHadoopUtil.get.globPath(fs, Path.mergePaths(validated(basep), validated(globp))),
    hadoopConf = conf,
    filter = null,
    sparkSession = spark)

  fileCatalog.flatMap(_._2.map(_.path))
}

val root = "/mnt/path/table"
val globp = "[^_]*" // glob pattern, e.g. "service=webapp/date=2019-03-31/*log4j*"

val files = listFiles(root, globp)
files.toDF("path").show()
```

```
+------------------------------------------------------------------------------------------------------------------------------+
|path                                                                                                                          |
+------------------------------------------------------------------------------------------------------------------------------+
|dbfs:/mnt/path/table/a=1/b=2/c=3/part-00000-tid-5046671251912249212-afa32967-b7db-444e-b895-d12d68c05500-5.c000.snappy.parquet|
|dbfs:/mnt/path/table/a=2/b=3/c=4/part-00001-tid-5046671251912249212-afa32967-b7db-444e-b895-d12d68c05500-6.c000.snappy.parquet|
|dbfs:/mnt/path/table/a=3/b=4/c=5/part-00002-tid-5046671251912249212-afa32967-b7db-444e-b895-d12d68c05500-7.c000.snappy.parquet|
|dbfs:/mnt/path/table/a=4/b=5/c=6/part-00003-tid-5046671251912249212-afa32967-b7db-444e-b895-d12d68c05500-8.c000.snappy.parquet|
+------------------------------------------------------------------------------------------------------------------------------+
```

`listFiles` 函数以 `base` 路径和 `glob` 路径作为参数，扫描文件并与 `glob` 模式匹配，然后以字符串序列的形式返回所有匹配的叶文件。

该函数还使用 `SparkHadoopUtil` 包中的另一个实用工具函数 `globPath`。 此函数列出具有指定前缀的目录中的所有路径，而不进一步列出叶子级（文件）。 路径列表将传递到 `InMemoryFileIndex.bulkListLeafFiles` 方法中，该方法是用于列出分布式文件的 Spark 内部 API。

这两个列表实用工具函数单独使用时优势并不明显，但它们结合使用时会带来很多好处：你可以使用 globPath 函数获得要列出的顶级目录的列表，该函数将在驱动程序上运行，并且可以使用 `bulkListLeafFiles` 将顶级目录的所有子叶的列表分发到 Spark 辅助角色中。

根据 Amdahl 定律，加速可以快 20-50 倍。 原因在于，你可以根据实际的文件物理布局轻松控制 `glob` 路径，并通过 `spark.sql.sources.parallelPartitionDiscovery.parallelism``for ``InMemoryFileIndex` 控制并行度。

## <a name="delete-files"></a>删除文件

从非托管表中删除文件或分区时，可以使用 Azure Databricks 实用工具函数 `dbutils.fs.rm`。 此函数利用本机云存储文件系统 API，该 API 针对所有文件操作进行了优化。 但是，由于存储请求速率限制、网络限制等原因，不能直接使用 `dbutils.fs.rm("path/to/the/table")` 删除大型表。

你可以使用以上脚本有效地列出文件。 对于较小的表，要删除的文件的收集路径适合驱动程序内存，因此可以使用 Spark 作业分发文件删除任务。

对于大型表，即使是单个顶级分区，文件路径的字符串表示形式也无法放入驱动程序内存中。 解决这个问题最简单的方法是以递归方式收集内部分区的路径，列出这些路径，并以并行方式删除它们。

```scala
import scala.util.{Try, Success, Failure}

def delete(p: String): Unit = {
  dbutils.fs.ls(p).map(_.path).toDF.foreach { file =>
    println(s"deleting file: $file")
    dbutils.fs.rm(file(0).toString, true)
  }
}

final def walkDelete(root: String)(level: Int): Unit = {
  dbutils.fs.ls(root).map(_.path).foreach { p =>
    println(s"Deleting: $p, on level: ${level}")
    val deleting = Try {
      if(level == 0) delete(p)
      else if(p endsWith "/") walkDelete(p)(level-1)
      //
      // Set only n levels of recursion, so it won't be a problem
      //
      else delete(p)
    }
    deleting match {
      case Success(v) => {
        println(s"Successfully deleted $p")
        dbutils.fs.rm(p, true)
      }
      case Failure(e) => println(e.getMessage)
    }
  }
}
```

此代码删除内部分区，同时确保要删除的分区足够小。 它通过按每个级别递归地搜索分区来实现此功能，并且只在到达所设置的级别时才开始删除。 例如，你希望首先删除顶级分区，需使用 `walkDelete(root)(0)`。 Spark 将删除 `dbfs:/mnt/path/table/a=1/` 下的所有文件，然后删除 `.../a=2/`，依此类推。

Spark 作业使用上面所示的 `delete` 函数分发删除任务，列出带有 `dbutils.fs.ls` 的文件，并假设此级别的子分区数量很少。 你还可以通过将 `dbutils.fs.ls` 函数替换为上面所示的 `listFiles` 函数来提高效率，只需稍作修改。

## <a name="summary"></a>总结

上面的两种方法展示了如何通过列出和删除大型表来划分和解决问题。 它们使用一些 Spark 实用工具函数和特定于 Azure Databricks 环境的函数。 即使不能直接使用它们，也可以创建自己的实用工具函数，以类似的方式解决问题。