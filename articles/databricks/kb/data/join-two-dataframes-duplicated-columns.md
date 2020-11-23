---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/10/2020
title: 防止在联接两个数据帧时出现重复列 - Azure Databricks
description: 了解如何防止在 Azure Databricks 中联接两个数据帧时出现重复列。
ms.openlocfilehash: be8e6c31b91360da14b9cb5397dc890a5d386263
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589756"
---
# <a name="prevent-duplicated-columns-when-joining-two-dataframes"></a>防止在联接两个数据帧时出现重复列

如果在 Spark 中执行联接，但未正确指定联接，最后会得到重复的列名。 这使得选择这些列变得更难。 本文和笔记本演示如何执行联接，以避免出现重复列。

## <a name="join-on-columns"></a>联接列

如果联接列，则会得到重复列。

**Scala**

```scala
%scala

val llist = Seq(("bob", "2015-01-13", 4), ("alice", "2015-04-23",10))
val left = llist.toDF("name","date","duration")
val right = Seq(("alice", 100),("bob", 23)).toDF("name","upload")

val df = left.join(right, left.col("name") === right.col("name"))
```

**Python**

```python
%python

llist = [('bob', '2015-01-13', 4), ('alice', '2015-04-23',10)]
left = spark.createDataFrame(llist, ['name','date','duration'])
right = spark.createDataFrame([('alice', 100),('bob', 23)],['name','upload'])

df = left.join(right, left.name == right.name)
```

## <a name="solution"></a>解决方案

将联接列指定为数组类型或字符串。

**Scala**

```scala
%scala

val df = left.join(right, Seq("name"))
```

```scala
%scala

val df = left.join(right, "name")
```

**Python**

```python
%python
df = left.join(right, ["name"])
```

```python
%python
df = left.join(right, "name")
```

**R**

首先将数据帧注册为表。

```python
%python

left.createOrReplaceTempView("left_test_table")
right.createOrReplaceTempView("right_test_table")
```

```r
%r
library(SparkR)
sparkR.session()
left <- sql("SELECT * FROM left_test_table")
right <- sql("SELECT * FROM right_test_table")
```

上面的代码会生成重复列。 下面的代码则不会。

```r
%r
head(drop(join(left, right, left$name == right$name), left$name))
```

### <a name="join-dataframes-with-duplicated-columns-notebook"></a>将数据帧与重复列笔记本联接起来

[获取笔记本](../_static/notebooks/data/join-two-dataframes-duplicated-column-notebook.html)