---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/10/2020
title: 如何通过 Bucket 存储功能提高性能 - Azure Databricks
description: 了解如何使用 Bucket 存储功能来提高 Azure Databricks 性能。
ms.openlocfilehash: 4b2446f22e6ee438e40909f88e8502916e73686c
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589606"
---
# <a name="how-to-improve-performance-with-bucketing"></a>如何通过 Bucket 存储功能提高性能

Bucket 存储功能是 Apache Spark SQL 中的一种优化技术。 根据从一个或多个 Bucket 存储列派生的值，在指定数量的 Bucket 之间分配数据。  Bucket 存储功能可在下游操作（如表联接）之前混排和排序数据，从而提高性能。 要权衡的是因混排和排序而产生的初始开销，但对于某些数据转换，该技术可避免之后的混排和排序，从而提高性能。

该技术对维度表非常有用，维度表是包含主键的常用表。 当经常有涉及大型表和小型表的联接操作时，它也非常有用。

下面的示例笔记本显示了执行由 Bucket 存储的表和未由 Bucket 存储的表的联接时物理计划中的差异。

## <a name="bucketing-example-notebook"></a>Bucket 存储示例笔记本

[获取笔记本](../_static/notebooks/data/bucketing-example.html)