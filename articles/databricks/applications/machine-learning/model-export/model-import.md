---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/10/2020
title: 将模型导入应用程序 - Azure Databricks
description: 了解如何使用 Databricks ML 模型导出来导入机器学习模型。
ms.openlocfilehash: c9bfab6722afe994a687825eeb02d6eac5123eac
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589928"
---
# <a name="import-models-into-your-application"></a><a id="import-models-into-your-application"> </a><a id="model-import"> </a>将模型导入应用程序

本文讨论 [Databricks ML 模型导出](model-export-import.md#model-export-import)工作流的导入和评分部分；请参阅[导出Apache Spark ML 模型和管道](model-export.md)，了解工作流的导出部分。

若要使用通过 Databricks ML 模型导出导出的模型，请在库 `dbml-local` 中调用 API。
此库提供用于导入模型和执行低延迟评分（预测或推理）的 Scala 和 Java API。

## <a name="using-saved-models-in-java-applications"></a>在 Java 应用程序中使用已保存的模型

假设已导出逻辑回归管道，并将其保存在 `my_models/lr_pipeline` 下。 可以使用 `ModelFactory` 从已保存的模型的目录创建 `LocalModel`，并对新数据进行评分。

```java
// Load exported model
String modelPath = "my_models/lr_pipeline";
LocalModel model = ModelFactory.loadModel(modelPath);

// The model input is a standard JSON string.
// The input schema here is: [origLabel: Double, features: Vector].
String input =
  "{\"origLabel\":-1.0," +
  "\"features\":{\"type\":0,\"size\":13," +
  "\"indices\":[0,2,3,4,6,7,8,9,10,11,12]," +
  "\"values\":[74.0,2.0,120.0,269.0,2.0,121.0,1.0,0.2,1.0,1.0,3.0]}" +
  "}";

// The model output is also a standard JSON string, with the expected output fields.
String output = model.transform(input);
```

输入接受 Apache Spark 数据集和数据帧使用 `Dataset.toJSON` 方法生成的相同 JSON 格式（请参阅[数据集 API 文档](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.Dataset)）。
有关更多详细信息，请参阅 [dbml-local API 文档](https://databricks.github.io/databricks-ml/latest/)。

## <a name="specifying-the-dbml-local-library-dependency-in-maven"></a>在 Maven 中指定 `dbml-local` 库依赖项

可以像指定其他依赖项一样，使用 Maven 坐标指定应用程序的 `dbml-local` 库依赖项。  下面的代码片段举例说明了如何在 Maven 项目 `pom.xml` 生成文件中包含 `dbml-local`。

```xml
<!-- Add repository for dbml-local dependency -->
 <repositories>
   <repository>
     <snapshots>
       <enabled>false</enabled>
     </snapshots>
     <id>bintray-databricks-maven</id>
     <name>bintray</name>
     <url>https://dl.bintray.com/databricks/maven</url>
   </repository>
 </repositories>

 <dependencies>
   <!-- Main dependency for Model Scoring -->
   <dependency>
     <groupId>com.databricks</groupId>
     <artifactId>dbml-local</artifactId>
     <version>0.2.2-spark2.2</version>
   </dependency>
 </dependencies>
```

## <a name="downloading-dbml-local-jars"></a>下载 `dbml-local` JAR

可以从 [bintray](https://dl.bintray.com/databricks/maven/com/databricks/dbml-local/) 获取 `dbml-local` JAR。

## <a name="dbml-local-license"></a>`dbml-local` 许可证

`dbml-local` 库发布在 [MIT 许可证](https://opensource.org/licenses/mit-license.php)下。

## <a name="example-application"></a>示例应用程序

可以查看一个非常简单的示例应用程序，该应用程序演示了如何在 [databricks-ml-examples Github 存储库](https://github.com/databricks/databricks-ml-examples/tree/master/model-export-demo)中使用 Databricks ML 模型导出助理库 `dbml-local`。
此演示包含用于训练和导出 MLlib 模型的 Databricks 笔记本。
这些笔记本与简单的 Java 应用程序配对，它们显示了如何导入模型并进行预测。