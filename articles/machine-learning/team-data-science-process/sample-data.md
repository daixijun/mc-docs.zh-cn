---
title: 不同 Azure 存储位置中的示例数据 - Team Data Science Process
description: Azure blob 容器、SQL Server 和 Hive 表中的示例数据，可将其缩小为更小但更具代表性且更易于管理的大小。
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 31f4e4c58738c0e6c3bcf303f11e12dab9c65766
ms.sourcegitcommit: c2c9dc65b886542d220ae17afcb1d1ab0a941932
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94978243"
---
# <a name="sample-data-in-azure-blob-containers-sql-server-and-hive-tables"></a><a name="heading"></a>对 Azure Blob 容器、SQL Server 和 Hive 表中的数据采样

以下文章介绍了如何对存储在三个不同 Azure 位置之一的数据进行采样：

* [**Azure Blob 容器数据**](sample-data-blob.md)的采样方法是先以编程方式下载该数据，并使用样本 Python 代码对其采样。
* [**SQL Server 数据**](sample-data-sql-server.md)是使用 SQL 和 Python 编程语言进行采样。 
* [**Hive 表数据**](sample-data-hive.md)是使用 Hive 查询进行采样。

此采样任务是[团队数据科学流程 (TDSP)](/machine-learning/team-data-science-process/) 中的一个步骤。

**为什么对数据采样？**

如果计划要分析的数据集很大，通常最好是对数据进行向下采样，以将数据减至较小但具备代表性且更易于管理的规模。 缩小可能有利于数据理解、探索和特征工程。 这一采样角色在 Cortana Analytics 进程中的作用是能够快速建立数据处理函数和机器学习模型的快速原型。