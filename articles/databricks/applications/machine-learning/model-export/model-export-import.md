---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/10/2020
title: Databricks ML 模型导出 - Azure Databricks
description: 了解如何使用 Databricks ML 模型导出来导出和导入机器学习模型。
ms.openlocfilehash: ea199281ec32d1b074a023d123311d963120a732
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589929"
---
# <a name="databricks-ml-model-export"></a><a id="databricks-ml-model-export"> </a><a id="model-export-import"> </a>Databricks ML 模型导出

> [!IMPORTANT]
>
> Databricks ML 模型导出已在 [Databricks Runtime 5.3（不受支持）](../../../release-notes/runtime/5.3.md)中弃用，而且已从 [Databricks Runtime 6.0（不受支持）](../../../release-notes/runtime/6.0.md)中删除。 请改用 [MLeap](mleap-model-export.md#mleap-model-export) 来导入和导出模型。

Databricks ML 模型导出会从 Apache Spark 导出模型和完整的 ML 管道。
可将这些导出的模型和管道导入到其他平台，以进行评分和预测。

模型导出面向低延迟、轻型且支持 ML 的应用程序 。 通过模型导出，你可以：

* 使用现有模型部署系统
* 实现非常低的延迟（毫秒）
* 在自定义部署中使用 ML 模型和管道

评分（推理）库采用 JSON 编码的功能。

```javascript
{"id":5923937,  // any metadata
"features:": { // MLlib vector format: 0 for sparse vector, 1 for dense vector
   "type": 1,
   "values":[0.1, 1.3, 8.4, 4.2]}}
```

结果也以 JSON 编码。

```javascript
{"id":5923937,
 "prediction": 1.0}
```

* [导出 Apache Spark ML 模型和管道](model-export.md)
  * [模型导出格式](model-export.md#model-export-format)
  * [支持的模型](model-export.md#supported-models)
  * [从 Databricks 导出模型](model-export.md#exporting-models-from-databricks)
    * [模型导出 Scala 笔记本](model-export.md#model-export-scala-notebook)
    * [模型导出 Python 笔记本](model-export.md#model-export-python-notebook)
* [将模型导入应用程序](model-import.md)
  * [在 Java 应用程序中使用已保存的模型](model-import.md#using-saved-models-in-java-applications)
  * [在 Maven 中指定 `dbml-local` 库依赖项](model-import.md#specifying-the-dbml-local-library-dependency-in-maven)
  * [下载 `dbml-local` JAR](model-import.md#downloading-dbml-local-jars)
  * [`dbml-local` 许可证](model-import.md#dbml-local-license)
  * [示例应用程序](model-import.md#example-application)
* [版本控制](versioning.md)
  * [支持的 Databricks Runtime 版本](versioning.md#supported-databricks-runtime-versions)
  * [Databricks ML 模型导出版本控制](versioning.md#databricks-ml-model-export-versioning)
    * [Apache Spark MLlib 版本策略](versioning.md#apache-spark-mllib-version-policy)
    * [Databricks ML 模型导出保证](versioning.md#databricks-ml-model-export-guarantees)