---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 07/14/2020
title: Apache Spark 入门 - Azure Databricks
description: 了解如何使用 Azure Databricks 数据集编写 Apache Spark 应用程序。
ms.openlocfilehash: 6f2e9eca638a718a4d00ba9b1c6920e12dcb0b17
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589639"
---
# <a name="get-started-with-apache-spark"></a>Apache Spark 入门

本教程模块可帮助你快速开始使用 Apache Spark。 我们简要讨论关键概念，以便你可以快速开始编写第一个 Apache Spark 应用程序。 在本指南中的其他教程模块中，你将有机会更深入地了解所选文章的内容。

本教程将介绍以下内容：

* [关键 Apache Spark 接口](#spark-data-interfaces)
* [如何编写第一个 Apache Spark 应用程序](#first-spark-app)
* [如何访问预加载的 Azure Databricks 数据集](#qs-databricks-datasets)

我们还提供了一个[示例笔记本](#notebook-qs)，你可以导入该笔记本以访问并运行模块中包含的所有代码示例。

## <a name="requirements"></a>要求

完整[快速入门：使用 Azure 门户在 Azure Databricks 上运行 Spark 作业](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)。

## <a name="spark-interfaces"></a><a id="spark-data-interfaces"> </a><a id="spark-interfaces"> </a>Spark 界面

你应该了解三个关键的 Apache Spark 接口：可复原的分布式数据集、数据帧和数据集。

* 可复原分布式数据库：第一个 Apache Spark 抽象是可复原分布式数据集 (RDD)。 它是一系列数据对象的接口，由位于计算机集合（群集）中的一个或多个类型组成。 RDD 可以通过多种方式创建，并且是可用的“最低级别”API。 虽然这是 Apache Spark 的原始数据结构，但你应该关注数据帧 API，它是 RDD 功能的超集。 可在 Java、Python 和 Scala 语言中使用 RDD API。
* 数据帧：这些在概念上类似于你可能熟悉的 [pandas](https://pandas.pydata.org/) Python 库和 [R 语言](https://www.r-project.org/)中的数据帧。 可在 Java、Python、R 和 Scala 语言中使用数据帧 API。
* 数据集：数据帧和 RDD 的组合。 它提供 RDD 中可用的类型化接口，同时提供数据帧的便利性。 可在 Java 和 Scala 语言中使用数据集 API。

在许多情况下，特别是在数据帧和数据集中嵌入了性能优化的情况下，不需要使用 RDD。 但了解 RDD 抽象非常重要，因为：

* RDD 是底层基础结构，使 Spark 能极速运行并提供数据世系。
* 如果要深入了解更高级的 Spark 组件，可能需要使用 RDD。
* [Spark UI](../../clusters/clusters-manage.md#clusters-sparkui) 中的可视化效果引用 RDD。

开发 Spark 应用程序时，通常使用[数据帧教程](dataframes.md#spark-dataframes)和[数据集教程](datasets.md#spark-datasets)。

## <a name="write-your-first-apache-spark-application"></a><a id="first-spark-app"> </a><a id="write-your-first-apache-spark-application"> </a>编写第一个 Apache Spark 应用程序

若要编写第一个 Apache Spark 应用程序，请将代码添加到 Azure Databricks 笔记本的单元格中。 本示例使用 Python。 有关详细信息，还可以参考 [Apache Spark 快速入门指南](https://spark.apache.org/docs/latest/quick-start.html)。

此第一个命令列出 [Databricks 文件系统](../../data/databricks-file-system.md)中的文件夹的内容：

```python
# Take a look at the file system
display(dbutils.fs.ls("/databricks-datasets/samples/docs/"))
```

> [!div class="mx-imgBorder"]
> ![文件夹内容](../../_static/images/getting-started/dbfs-readme-sm.png)

下一条命令使用 `spark`、每个笔记本中可用的 `SparkSession`，来读取 `README.md` 文本文件并创建名为 `textFile` 的数据帧：

```python
textFile = spark.read.text("/databricks-datasets/samples/docs/README.md")
```

若要计算文本文件的行数，请将 `count` 操作应用于数据帧：

```python
textFile.count()
```

> [!div class="mx-imgBorder"]
> ![文本文件行计数](../../_static/images/getting-started/databricks-guide-textfile-count-output.png)

有一点需要注意，读取文本文件不会生成任何输出，而第三个命令，即执行 `count`，会生成输出。 出现这种情况的原因是，第一个命令是一个“转换”，而第二个命令是一个“操作” 。 转换有延迟，并仅在运行操作时运行。 这允许 Spark 优化性能（例如，在联接之前运行筛选器），而不是以串行方式运行命令。 有关转换和操作的完整列表，请参阅 Apache Spark 编程指南：[转换](https://spark.apache.org/docs/latest/rdd-programming-guide.html#transformations)和[操作](https://spark.apache.org/docs/latest/rdd-programming-guide.html#actions)。

## <a name="azure-databricks-datasets"></a><a id="azure-databricks-datasets"> </a><a id="qs-databricks-datasets"> </a>Azure Databricks 数据集

Azure Databricks 包括工作区中可用于学习 Spark 或测试算法的各种[数据集](../../data/databricks-datasets.md#databricks-datasets)。 整个入门指南中会不断提到这些数据集。 可在 `/databricks-datasets` 文件夹中找到这些数据集。

## <a name="notebooks"></a><a id="notebook-qs"> </a><a id="notebooks"> </a>笔记本

若要访问这些代码示例，请导入以下笔记本之一。

### <a name="apache-spark-quick-start-python-notebook"></a>Apache Spark Python 快速入门笔记本

[获取笔记本](../../_static/notebooks/getting-started/quickstartusingpython.html)

### <a name="apache-spark-quick-start-scala-notebook"></a>Apache Spark 快速入门 Scala 笔记本

[获取笔记本](../../_static/notebooks/getting-started/quickstartusingscala.html)