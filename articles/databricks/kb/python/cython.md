---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 安装和编译 Cython - Azure Databricks
description: 了解如何通过 Azure Databricks 安装和编译 Cython。
ms.openlocfilehash: 2a56b53952fb02012cf30d275c61107288b639b9
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589792"
---
# <a name="install-and-compile-cython"></a><a id="cython"> </a><a id="install-and-compile-cython"> </a>安装和编译 Cython

本文档介绍如何使用已编译的 Cython 代码运行 Spark 代码。 步骤如下：

1. 在 [DBFS](/databricks/data/dbfs-databricks-file-system) 上创建一个示例 cython 模块。
2. 将文件添加到 SparkSession。
3. 创建包装方法以在执行器上加载模块。
4. 在示例数据集上运行映射器。
5. 生成更大的数据集，并将性能与本机 Python 示例进行比较。

> [!NOTE]
>
> 默认情况下，如果未引用任何协议，路径将使用 `dbfs:/`。

```python
# Write an example cython module to /example/cython/fib.pyx in DBFS.
dbutils.fs.put("/example/cython/fib.pyx", """
def fib_mapper_cython(n):
    '''
    Return the first fibonnaci number > n.
    '''
    cdef int a = 0
    cdef int b = 1
    cdef int j = int(n)
    while b<j:
        a, b  = b, a+b
    return b, 1
""", True)

# Write an example input file to /example/cython/input.txt in DBFS.
# Every line of this file is an integer.
dbutils.fs.put("/example/cython_input/input.txt", """
1
10
100
""", True)

# Take a look at the example input.
dbutils.fs.head("/example/cython_input/input.txt")
```

## <a name="add-cython-source-files-to-spark"></a>将 Cython 源文件添加到 Spark

为了使 Cython 源文件在群集中可用，我们将使用 `sc.addPyFile` 将这些文件添加到 Spark 中。 例如，

```python
sc.addPyFile("dbfs:/example/cython/fib.pyx")
```

## <a name="test-cython-compilation-on-the-driver-node"></a>在驱动程序节点上测试 Cython 编译

此代码将首先在驱动程序节点上测试编译。

```python
import pyximport
import os

pyximport.install()
import fib
```

## <a name="define-the-wapper-function-to-compile-and-import-the-module"></a>定义 wapper 函数以编译和导入模块

将在执行器节点上执行打印语句。 可以查看 stdout 日志消息来跟踪模块的进度。

```python
import sys, os, shutil, cython

def spark_cython(module, method):
  def wrapped(*args, **kwargs):
    print 'Entered function with: %s' % args
    global cython_function_
    try:
      return cython_function_(*args, **kwargs)
    except:
      import pyximport
      pyximport.install()
      print 'Cython compilation complete'
      cython_function_ = getattr(__import__(module), method)
    print 'Defined function: %s' % cython_function_
    return cython_function_(*args, **kwargs)
  return wrapped
```

## <a name="run-the-cython-example"></a>运行 Cython 示例

下面的代码段在几个数据点上运行斐波那契示例。

```python
# use the CSV reader to generate a Spark DataFrame. Roll back to RDDs from DataFrames and grab the single element from the GenericRowObject
lines = spark.read.csv("/example/cython_input/").rdd.map(lambda y: y.__getitem__(0))

mapper = spark_cython('fib', 'fib_mapper_cython')
fib_frequency = lines.map(mapper).reduceByKey(lambda a, b: a+b).collect()
print fib_frequency
```

## <a name="performance-comparison"></a>性能比较

下面我们将测试这两个实现之间的速度差异。
我们将使用 `spark.range()` api 生成 10,000 到 100,000,000 的数据点，以及 50 个 Spark 分区。 我们会将此输出作为 CSV 写入 DBFS。

对于此测试，禁用[自动缩放](/databricks/clusters/configure#autoscaling)，以确保群集具有固定数量的 Spark 执行器。

```python
dbutils.fs.rm("/tmp/cython_input/", True)
spark.range(10000, 100000000, 1, 50).write.csv("/tmp/cython_input/")
```

### <a name="normal-pyspark-code"></a>正常 PySpark 代码

```python
def fib_mapper_python(n):
  a = 0
  b = 1
  print "Trying: %s" % n
  while b < int(n):
    a, b = b, a+b
  return (b, 1)

print fib_mapper_python(2000)

lines = spark.read.csv("/tmp/cython_input/").rdd.map(lambda y: y.__getitem__(0))
fib_frequency = lines.map(lambda x: fib_mapper_python(x)).reduceByKey(lambda a, b: a+b).collect()
print fib_frequency
```

### <a name="test-cython-code"></a>测试 Cython 代码

现在，测试已编译的 Cython 代码。

```python
lines = spark.read.csv("/tmp/cython_input/").rdd.map(lambda y: y.__getitem__(0))
mapper = spark_cython('fib', 'fib_mapper_cython')
fib_frequency = lines.map(mapper).reduceByKey(lambda a, b: a+b).collect()
print fib_frequency
```

我们生成的测试数据集有 50 个 Spark 分区，会创建 50 个 csv 文件，如下所示。 可以使用 `dbutils.fs.ls("/tmp/cython_input/")` 查看数据集。