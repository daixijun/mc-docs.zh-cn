---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 09/24/2020
title: 数据集教程 - Azure Databricks
description: 了解如何在 Azure Databricks 中使用 Apache Spark 数据集 API。
ms.openlocfilehash: 61974d2ba8cd79e215daaf28e7900a8bd8b79dba
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589641"
---
# <a name="datasets-tutorial"></a><a id="datasets-tutorial"> </a><a id="spark-datasets"> </a>数据集教程

Apache Spark [数据集 API](https://spark.apache.org/docs/latest/api/java/org/apache/spark/sql/Dataset.html) 提供了一个类型安全、面向对象的编程接口。 `DataFrame` 是非类型化的 `Dataset [Row]` 的别名。 数据集提供编译时类型安全性（这意味着可以在运行生产应用程序之前检查其中的错误），并且允许对用户定义的类进行直接操作。 数据集 API 还提供高级特定于域的语言操作（如 `sum()`、`avg()`、`join()`、`select()`、`groupBy()`），使代码更易于表达、读取和写入。

<!--Like DataFrames, Datasets take advantage of Spark’s Catalyst optimizer by exposing expressions and data fields to a query planner. Beyond Catalyst’s optimizer, Datasets also leverage Tungsten’s fast in-memory encoding. They extend these benefits with compile-time type safety—meaning production applications can be checked for errors before they are ran—and they also allow direct operations over user-defined classes, as you will see in a couple of simple examples.-->

本教程模块介绍如何执行以下操作：

* [创建示例数据](#create-sample-data)
* [加载示例数据](#load-ds-sample-data)
* [查看数据集](#view-dataset)
* [处理和可视化数据集](#process-and-visualize-dataset)

我们还提供了一个[示例笔记本](#dataset-notebook)，你可以导入该笔记本，访问并运行模块中包含的所有代码示例。

## <a name="create-sample-data"></a>创建示例数据

创建数据集的方法有两种：动态和使用 `SparkSession` 从 JSON 文件读取。 首先，对于示例或演示中的基元类型，可以在 Scala 或 Python 笔记本中或在示例 Spark 应用程序中创建数据集。 例如，下面是在笔记本中创建包含 100 个整数的数据集的方法。 我们使用 `spark` 变量创建 100 个整数作为 `Dataset[Long]`。

```scala
// range of 100 numbers to create a Dataset.
val range100 = spark.range(100)
range100.collect()
```

> [!div class="mx-imgBorder"]
> ![收集数据集](../../_static/images/getting-started/gsasg-dataset-output.png)

## <a name="load-sample-data"></a><a id="load-ds-sample-data"> </a><a id="load-sample-data"> </a>加载示例数据

更常用的方式是从外部数据源（如 HDFS、对象存储、NoSQL、RDBMS 或本地文件系统）读取数据文件。 Spark 支持多种格式：JSON、CSV、Text、Parquet、ORC 等。 若要读取 JSON 文件，还可以使用 `SparkSession` 变量 `spark`。

开始使用数据集的最简单方法是使用 Azure Databricks 工作区中可访问的 `/databricks-datasets` 文件夹中可用的 Azure Databricks 数据集示例。

```scala
val df = spark.read.json("/databricks-datasets/samples/people/people.json")
```

在读取 JSON 文件时，Spark 不知道你的数据结构。 也就是说，它不知道你希望如何将数据组织成类型特定的 JVM 对象。 它尝试从 JSON 文件推断架构，并创建泛型 `Row` 对象的 `DataFrame = Dataset[Row]`。

可以通过定义特定于域的 Scala `case class` 并将 DataFrame 转换为该类型，将 `DataFrame` 显式转换为反映 Scala 类对象的 `Dataset`：

```scala
// First, define a case class that represents a type-specific Scala JVM Object
case class Person (name: String, age: Long)

// Read the JSON file, convert the DataFrames into a type-specific JVM Scala object
// Person. At this stage Spark, upon reading JSON, created a generic
// DataFrame = Dataset[Rows]. By explicitly converting DataFrame into Dataset
// results in a type-specific rows or collection of objects of type Person
val ds = spark.read.json("/databricks-datasets/samples/people/people.json").as[Person]
```

可以对 JSON 文件中捕获的 IoT 设备状态信息进行类似操作：定义 `case class`、读取 JSON 文件并转换 `DataFrame = Dataset[DeviceIoTData]`。

将 `DataFrame` 转换为特定于类型的 JVM 对象有两个原因。 首先，在显式转换之后，对于使用数据集 API 的所有关系表达式和查询表达式，你会获得编译类型安全性。 例如，如果使用的筛选器操作所使用的数据类型是错误的，Spark 会检测到类型不匹配并发出编译错误信息而不是发出执行运行时错误信息，以便你更早获取错误信息。 其次，数据集 API 提供了高阶方法，这使代码更易于阅读和开发。 在[处理并可视化数据集](#process-and-visualize-dataset)部分中，请注意如何使用 `Dataset` 类型化对象使代码更易于表达和读取。

如在 `Person` 示例中，此处创建了一个封装 Scala 对象的 `case class`。  若要访问包含 IoT 数据的文件，请加载文件 `/databricks-datasets/iot/iot_devices.json`。

```scala
// define a case class that represents the device data.
case class DeviceIoTData (
  battery_level: Long,
  c02_level: Long,
  cca2: String,
  cca3: String,
  cn: String,
  device_id: Long,
  device_name: String,
  humidity: Long,
  ip: String,
  latitude: Double,
  longitude: Double,
  scale: String,
  temp: Long,
  timestamp: Long
)

// read the JSON file and create the Dataset from the ``case class`` DeviceIoTData
// ds is now a collection of JVM Scala objects DeviceIoTData
val ds = spark.read.json("/databricks-datasets/iot/iot_devices.json").as[DeviceIoTData]
```

## <a name="view-the-dataset"></a><a id="view-dataset"> </a><a id="view-the-dataset"> </a>查看数据集

若要以表格格式查看数据，而不是将其导出到第三方工具，可以使用 Azure Databricks `display()` 命令。 加载 JSON 数据并将其转换为类型特定的 JVM 对象的集合的 `Dataset` 后，可以使用 `display()` 或标准 Spark 命令（如 `take()`、`foreach()` 和 `println()` API 调用）查看它们，与查看 `DataFrame` 时一样。

```scala
// display the dataset table just read in from the JSON file
display(ds)
```

```scala
// Using the standard Spark commands, take() and foreach(), print the first
// 10 rows of the Datasets.
ds.take(10).foreach(println(_))
// Print first 10 rows of a dataset
```

> [!div class="mx-imgBorder"]
> ![打印 10 个数据集行](../../_static/images/getting-started/gsasg-dataset-display-10.png)

## <a name="process-and-visualize-the-dataset"></a><a id="process-and-visualize-dataset"> </a><a id="process-and-visualize-the-dataset"> </a>处理和可视化数据集

数据集具有转换和操作。 最重要的是高级域特定操作，例如 `sum()`、`select()`、`avg()`、`join()` 和 `union()`。 有关详细信息，请参阅 [Scala 数据集 API](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.Dataset)。

在本示例中，可以使用 `filter()`、`map()`、`groupBy()` 和 `avg()` 等所有高级别方法来创建新 `Datasets`。 值得注意的是，你可以像 `case class` 中定义的那样按属性的名称访问属性。 也就是说，使用点符号访问单个字段。 这样，就可以轻松地读取和写入代码。

```scala
// filter out all devices whose temperature exceed 25 degrees and generate
// another Dataset with three fields that of interest and then display
// the mapped Dataset
val dsTemp = ds.filter(d => d.temp > 25).map(d => (d.temp, d.device_name, d.cca3))
display(dsTemp)
```

> [!div class="mx-imgBorder"]
> ![显示筛选的数据集](../../_static/images/getting-started/gsasg-display-filtered-dataset.png)

```scala
// Apply higher-level Dataset API methods such as groupBy() and avg().
// Filter temperatures > 25, along with their corresponding
// devices' humidity, compute averages, groupBy cca3 country codes,
// and display the results, using table and bar charts
val dsAvgTmp = ds.filter(d => {d.temp > 25}).map(d => (d.temp, d.humidity, d.cca3)).groupBy($"_3").avg()

// display averages as a table, grouped by the country
display(dsAvgTmp)
```

> [!div class="mx-imgBorder"]
> ![显示平均值](../../_static/images/getting-started/gsasg-display-dataset-averages-as-a-table.png)

```scala
// Select individual fields using the Dataset method select()
// where battery_level is greater than 6. Note this high-level
// domain specific language API reads like a SQL query
display(ds.select($"battery_level", $"c02_level", $"device_name").where($"battery_level" > 6).sort($"c02_level"))
```

> [!div class="mx-imgBorder"]
> ![选择包含值的字段](../../_static/images/getting-started/gsasg-dataset-display-table-output.png)

下面是一个动画 gif，演示使用数据集和 Azure Databricks `display()` 命令可以多么迅速地在地图、表和图表之间转换。

> [!div class="mx-imgBorder"]
> ![表到图表](../../_static/images/getting-started/gsasg-example-of-databricks-visualizations.gif)

使用 Databricks `display()` 命令的另一个好处是，可以使用许多嵌入式可视化效果快速查看此数据。 例如，在新单元格中，可以发出 SQL 查询并单击地图以查看数据。 但首先，需要将数据集 `ds` 保存为表或临时视图。

```scala
// registering your Dataset as a temporary view to which you can issue SQL queries
ds.createOrReplaceTempView("iot_device_data")
```

将 DeviceIoTData 的 `Dataset` 保存为表或临时视图后，可以向其发出 SQL 查询。

```sql
%sql select cca3, count (distinct device_id) as device_id from iot_device_data group by cca3 order by device_id desc limit 100
```

> [!div class="mx-imgBorder"]
> ![查询数据集作为表](../../_static/images/getting-started/gsasg-screenshot-of-dataset-map.png)

## <a name="notebook"></a><a id="dataset-notebook"> </a><a id="notebook"> </a>笔记本

若要访问这些代码示例、可视化效果等，请导入以下笔记本。 有关数据集的详细信息，请参阅[数据帧和数据集](../../spark/latest/dataframes-datasets/index.md)。

### <a name="apache-spark-datasets-notebook"></a>Apache Spark 数据集笔记本

[获取笔记本](../../_static/notebooks/getting-started/iotdevicegeoipds.html)