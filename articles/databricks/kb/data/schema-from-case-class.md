---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/10/2020
title: 从 Case 类生成架构 - Azure Databricks
description: 了解如何从 Scala Case 类生成架构。
ms.openlocfilehash: 0756471519b68589dace6806dadf7d75dcf686b3
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589563"
---
# <a name="generate-schema-from-case-class"></a>从 Case 类生成架构

Spark 提供了一种简单的方法来从 Scala Case 类生成架构。 对于 Case 类 `A`，请使用方法 `ScalaReflection.schemaFor[A].dataType.asInstanceOf[StructType]`。 例如：

```scala
%scala
import org.apache.spark.sql.types.StructType
import org.apache.spark.sql.catalyst.ScalaReflection

case class A(key: String, time: java.sql.Timestamp, date: java.sql.Date, decimal: java.math.BigDecimal, map: Map[String, Int], nested: Seq[Map[String, Seq[Int]]])
val schema = ScalaReflection.schemaFor[A].dataType.asInstanceOf[StructType]
schema.printTreeString
```