---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 09/24/2020
title: 数据帧教程 - Azure Databricks
description: 了解如何在 Azure Databricks 中使用 Apache Spark 数据帧 API。
ms.openlocfilehash: 948b1b07b5e2be13b846ed7e109fbe672617703d
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589644"
---
# <a name="dataframes-tutorial"></a><a id="dataframes-tutorial"> </a><a id="spark-dataframes"> </a>数据帧教程

Apache Spark [数据帧 API](https://spark.apache.org/docs/latest/api/python/pyspark.sql.html?highlight=dataframe#pyspark.sql.DataFrame) 提供了一组丰富的功能（选择列、筛选、联接、聚合等），可让你有效地解决常见的数据分析问题。 数据帧还允许通过自定义 Python、R、Scala 和 SQL 代码无缝地混合操作。 本教程模块介绍如何执行以下操作：

* [加载示例数据](#load-df-sample-data)
* [查看数据帧](#view-dataframe)
* [运行 SQL 查询](#run-sql)
* [可视化数据帧](#visualize-dataframe)

我们还提供了一个[示例笔记本](#dataframe-notebook)，你可以导入该笔记本，访问并运行模块中包含的所有代码示例。

## <a name="load-sample-data"></a><a id="load-df-sample-data"> </a><a id="load-sample-data"> </a>加载示例数据

若要开始使用数据帧，最简单的方法是使用 Azure Databricks 工作区中可访问的 `/databricks-datasets` 文件夹中提供的 Azure Databricks 数据集示例。 若要访问将城市人口与房屋售价中值进行比较的文件，请加载文件 `/databricks-datasets/samples/population-vs-price/data_geo.csv`。

示例笔记本是 SQL 笔记本，因此接下来的几个命令使用 `%python` [magic 命令](../../notebooks/notebooks-use.md#language-magic)。

```python
# Use the Spark CSV datasource with options specifying:
# - First line of file is a header
# - Automatically infer the schema of the data
%python
data = spark.read.csv("/databricks-datasets/samples/population-vs-price/data_geo.csv", header="true", inferSchema="true")
data.cache() # Cache data for faster reuse
data = data.dropna() # drop rows with missing values
```

## <a name="view-the-dataframe"></a><a id="view-dataframe"> </a><a id="view-the-dataframe"> </a>查看数据帧

现在你已创建 `data` 数据帧，接下来可使用标准 Spark 命令（例如 `take()`）快速访问数据。 例如，可使用命令 `data.take(10)` 查看 `data` 数据帧的前 10 行。

```python
%python
data.take(10)
```

> [!div class="mx-imgBorder"]
> ![数据帧采用](../../_static/images/getting-started/gsasg-output-of-the-dataframe-take-command.png)

若要以表格格式查看此数据，可使用 Azure Databricks `display()` 命令，而不是将数据导出到第三方工具。

```python
%python
display(data)
```

> [!div class="mx-imgBorder"]
> ![显示数据帧](../../_static/images/getting-started/gsasg-display-dataframe.png)

## <a name="run-sql-queries"></a><a id="run-sql"> </a><a id="run-sql-queries"> </a>运行 SQL 查询

发出 SQL 查询之前，必须先将 `data` 数据帧保存为表或临时视图：

```python
# Register table so it is accessible via SQL Context
%python
data.createOrReplaceTempView("data_geo")
```

然后在新单元中指定 SQL 查询，按州列出 2015 年售价中值：

```sql
select `State Code`, `2015 median sales price` from data_geo
```

> [!div class="mx-imgBorder"]
> ![SQL 查询 1](../../_static/images/getting-started/gsasg-visualize-dataframe-in-a-table.png)

或者，查询华盛顿州的估算人口：

```sql
select City, `2014 Population estimate` from data_geo where `State Code` = 'WA';
```

> [!div class="mx-imgBorder"]
> ![SQL 查询 2](../../_static/images/getting-started/gsasg-dataframe-query.png)

## <a name="visualize-the-dataframe"></a><a id="visualize-dataframe"> </a><a id="visualize-the-dataframe"> </a>直观呈现数据帧

使用 Azure Databricks `display()` 命令的另一个好处是，可使用许多嵌入式可视化效果快速查看此数据。 单击![图表按钮](../../_static/images/notebooks/chart-button.png)旁边的向下箭头来显示一个可视化效果类型列表：

> [!div class="mx-imgBorder"]
> ![将数据帧显示为条形图](../../_static/images/notebooks/display-charts.png)

然后，选择地图图标，创建上一部分的售价 SQL 查询的地图可视化效果：

> [!div class="mx-imgBorder"]
> ![将数据帧显示为地图](../../_static/images/getting-started/gsasg-visualize-dataframe-on-a-map.png)

## <a name="notebook"></a><a id="dataframe-notebook"> </a><a id="notebook"> </a>笔记本

若要运行这些代码示例和可视化效果等，请导入以下笔记本。 有关数据帧的详细信息，请参阅[数据帧和数据集](../../spark/latest/dataframes-datasets/index.md)。

### <a name="apache-spark-dataframes-notebook"></a>Apache Spark 数据帧笔记本

[获取笔记本](../../_static/notebooks/getting-started/popvspricemultichart.html)